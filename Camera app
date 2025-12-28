import cv2
import subprocess
import datetime
import time
import threading
from flask import Flask, render_template_string, Response
from picamera2 import Picamera2
from gpiozero import Button, LED

app = Flask(__name__)
picam2 = Picamera2()

# --- HARDWARE CONFIG ---
shutter_btn = Button(23)
focus_up_btn = Button(25)
focus_down_btn = Button(12)
status_led = LED(24)

current_focus = 2.0
is_capturing = False # Prevents focus changes during a photo

def start_preview():
    global picam2
    config = picam2.create_video_configuration(main={"format": "RGB888", "size": (320, 240)})
    picam2.configure(config)
    picam2.start()
    picam2.set_controls({"LensPosition": float(current_focus)})

start_preview()

# --- SMOOTH FOCUS LOOP ---
# This runs in the background to handle "holding down" the buttons
def focus_loop():
    global current_focus
    while True:
        if not is_capturing:
            changed = False
            if focus_up_btn.is_pressed:
                current_focus = min(12.0, round(current_focus + 0.1, 1))
                changed = True
            elif focus_down_btn.is_pressed:
                current_focus = max(0.0, round(current_focus - 0.1, 1))
                changed = True
            
            if changed:
                try:
                    picam2.set_controls({"LensPosition": current_focus})
                except:
                    pass
                time.sleep(0.05) # Adjust this to change focus "speed"
        time.sleep(0.01)

# Start the focus listener thread
threading.Thread(target=focus_loop, daemon=True).start()

# --- PHOTO LOGIC ---
def take_hq_photo():
    global picam2, current_focus, is_capturing
    is_capturing = True
    status_led.on()
    
    picam2.stop()
    picam2.close()
    time.sleep(0.2)
    
    filename = datetime.datetime.now().strftime("portrait_%H%M%S.jpg")
    cmd = (f"rpicam-still --width 4624 --height 3472 --timeout 1000 "
           f"--encoding jpg --quality 100 --lens-position {current_focus} "
           f"--output {filename}")
    
    subprocess.run(cmd, shell=True)
    
    status_led.off()
    picam2 = Picamera2()
    start_preview()
    is_capturing = False

# Physical Shutter Trigger
shutter_btn.when_pressed = take_hq_photo

# --- WEB UI ---
HTML = """
<html>
    <head>
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <style>
            body { text-align:center; background:#111; color:white; font-family:sans-serif; margin:0; padding:10px; }
            .btn { padding:20px; width:90%; background:red; color:white; border-radius:15px; font-weight:bold; font-size:18px; border:none; }
            .slider { width:90%; height:35px; margin:15px 0; }
        </style>
        <script>
            // Sync the slider and force refresh after capture
            setInterval(async () => {
                let r = await fetch('/get_status');
                let data = await r.json();
                document.getElementById('f-val').innerHTML = data.focus;
                document.getElementById('f-slider').value = data.focus;
                
                // If capture just finished, reset the image src to start the stream
                if (window.wasCapturing && !data.capturing) {
                    refreshStream();
                }
                window.wasCapturing = data.capturing;
            }, 500);

            function refreshStream() {
                const img = document.getElementById('stream');
                img.src = "/video_feed?t=" + new Date().getTime();
            }

            async function webCapture() {
                document.getElementById('shutter-btn').innerHTML = "CAPTURING...";
                await fetch('/capture');
                document.getElementById('shutter-btn').innerHTML = "CAPTURE PHOTO";
            }
        </script>
    </head>
    <body>
        <h1 style="color:#ff4444; font-size:20px;">64MP Hybrid Cam</h1>
        <img id="stream" src="/video_feed" style="width:100%; max-width:500px; border-radius:10px; border:2px solid #333;">
        <div style="margin:10px; background:#222; padding:10px; border-radius:10px;">
            <p>Focus: <span id="f-val">2.0</span></p>
            <input type="range" id="f-slider" class="slider" min="0" max="12" step="0.1" value="2.0" 
                   oninput="fetch('/focus/' + this.value)">
        </div>
        <button id="shutter-btn" class="btn" onclick="webCapture()">CAPTURE PHOTO</button>
    </body>
</html>
"""

@app.route('/')
def index(): return render_template_string(HTML)

@app.route('/get_status')
def get_status(): 
    return {"focus": current_focus, "capturing": is_capturing}

@app.route('/focus/<val>')
def focus(val):
    global current_focus
    current_focus = float(val)
    picam2.set_controls({"LensPosition": current_focus})
    return "OK"

@app.route('/capture')
def capture():
    take_hq_photo()
    return "OK"

@app.route('/video_feed')
def video_feed():
    def gen():
        while True:
            try:
                frame = picam2.capture_array()
                _, b = cv2.imencode('.jpg', cv2.cvtColor(frame, cv2.COLOR_RGB2BGR))
                yield (b'--frame\r\nContent-Type: image/jpeg\r\n\r\n' + b.tobytes() + b'\r\n')
            except:
                time.sleep(0.2)
                continue
    return Response(gen(), mimetype='multipart/x-mixed-replace; boundary=frame')

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8000, threaded=True)
