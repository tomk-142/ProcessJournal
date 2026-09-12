### Heart Rate Monitor Test

---

## This is the code I used to test my HW-827. The code is made using python and ran in CMD prompt just for the simplicity factor.

---

    import serial
    import matplotlib.pyplot as plt
    import matplotlib.animation as animation
    from collections import deque

    PORT = "COM7"      # change to match your board's current port
    BAUD = 115200
    WINDOW_SIZE = 300   # how many samples to show at once

    ser = serial.Serial(PORT, BAUD, timeout=1)
    ser.reset_input_buffer()

    # Tell the PulseSensor sketch to start streaming raw signal data
    ser.write(b'b')

    data = deque([0] * WINDOW_SIZE, maxlen=WINDOW_SIZE)

    fig, ax = plt.subplots(figsize=(10, 4))
    fig.patch.set_facecolor('#1a1a1a')
    ax.set_facecolor('#1a1a1a')

    line, = ax.plot(data, color='#ff3b5c', linewidth=2)
    ax.fill_between(range(WINDOW_SIZE), data, color='#ff3b5c', alpha=0.15)

    ax.set_title("Live Heart Rate Signal", color='white', fontsize=16, pad=15)
    ax.set_xlabel("Samples", color='white')
    ax.set_ylabel("Sensor Value", color='white')
    ax.tick_params(colors='white')
    for spine in ax.spines.values():
        spine.set_color('#444444')
    ax.set_xticks([])  # cleaner look for a presentation screenshot

    def update(frame):
        while ser.in_waiting:
            try:
                raw_line = ser.readline().decode('utf-8', errors='ignore').strip()
                value = int(raw_line)
                data.append(value)
            except ValueError:
                pass  # skip any non-numeric lines (like "BEAT!" text)

        line.set_ydata(data)
        ax.set_ylim(min(data) - 20, max(data) + 20)
        for coll in list(ax.collections):
            coll.remove()
        ax.fill_between(range(WINDOW_SIZE), data, color='#ff3b5c', alpha=0.15)
        return line,

    ani = animation.FuncAnimation(fig, update, interval=30, cache_frame_data=False)
    plt.tight_layout()
    plt.show()