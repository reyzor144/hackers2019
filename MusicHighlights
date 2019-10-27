import wave
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.ticker as ticker
import math
from flask import Flask
from flask import request
import json


def get_max(i, lst):
    while i < len(lst) and lst[i - 1] < lst[i]:
        i += 1
    if i == len(lst):
        return lst[i - 1]
    return lst[i]

def get_min(i, lst):
    while i < len(lst) and lst[i - 1] > lst[i]:
        i += 1
    if i == len(lst):
        return lst[i - 1]
    return lst[i]
types = {
    1: np.int8,
    2: np.int16,
    4: np.int32
}

def format_time(x, pos=None):
    global duration, nframes, k
    progress = int(x / float(nframes) * duration * k)
    mins, secs = divmod(progress, 60)
    hours, mins = divmod(mins, 60)
    out = "%d:%02d" % (mins, secs)
    if hours > 0:
        out = "%d:" % hours
    return out

app = Flask(__name__)


@app.route('/', methods=['POST'])
def main():
    data = 0
    if request.method == 'POST':
        data = request.data
    print(str(data)[2:-1])
    wav = wave.open(str(data)[2:-1], mode="r")
    (nchannels, sampwidth, framerate, nframes, comptype, compname) = wav.getparams()
    duration = nframes / framerate
    w, h = 800, 300
    k = nframes // w // 32
    DPI = 72
    peak = 256 ** sampwidth // 2
    print(duration)
    content = wav.readframes(nframes)
    samples = np.frombuffer(content, dtype=types[sampwidth])

    plt.figure(1, figsize=(float(w) / DPI, float(h) / DPI), dpi=DPI)
    plt.subplots_adjust(wspace=0, hspace=0)
    answer = set()
    for n in range(nchannels):
        channel = samples[n::nchannels]
        channel = channel[0::k]
        mmmm = max(channel)
        print(len(channel), channel[7000:7100], type(channel))
        if nchannels == 1:
            channel = channel - peak
        tt = int(duration)
        arr2 = [0] * tt
        psec = len(channel) // tt
        for i in range(int(duration)):
            arr2[i] = sum(map(lambda x: abs(x), channel[psec * i:psec * (i + 1)])) // tt

        for i in enumerate(arr2[:120]):
            print(i)
        moments = []
        for i in range(1, len(arr2) - 10):
            if arr2[i - 1] != 0 and (get_max(i, arr2) / arr2[i - 1] > 3 / 2 or get_min(i, arr2) / arr2[i - 1] < 2 / 3):
                moments.append(i - 1)
                if arr2[i] > arr2[i - 1]:
                    answer.add((-get_max(i, arr2) / arr2[i - 1] * (get_max(i, arr2) - arr2[i - 1]), i - 1))
                else:
                    answer.add((arr2[i - 1] / get_min(i, arr2) * (get_min(i, arr2) - arr2[i - 1]), i - 1))


    ans2 = list(set(map(lambda x: x[1], sorted(answer)[:20])))
    print(ans2)
    ans2.sort()
    stack = []
    for i in ans2:
        if len(stack) == 0:
            stack.append(i)
        elif len(stack) > 0 and stack[-1] - i < -5:
            stack.append(i)
    print(*stack)
    return json.encoder.c_encode_basestring(str(stack)[1:-1])

if __name__ == '__main__':
    app.run()
