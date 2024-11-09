# Srt-caption-maker

pip install moviepy pysrt

import tkinter as tk
from tkinter import filedialog, messagebox
import moviepy.editor as mp
import pysrt

class SubtitleMaker:
    def __init__(self, root):
        self.root = root
        self.root.title("Subtitle Maker")
        
        # Video selection and playback
        self.video_path = None
        self.subtitles = pysrt.SubRipFile()
        
        # Create UI components
        self.create_ui()
    
    def create_ui(self):
        # Video Selection Button
        self.load_button = tk.Button(self.root, text="Load Video", command=self.load_video)
        self.load_button.pack(pady=10)

        # Subtitle text entry
        tk.Label(self.root, text="Subtitle Text:").pack()
        self.subtitle_text = tk.Entry(self.root, width=40)
        self.subtitle_text.pack(pady=5)

        # Start and End time
        tk.Label(self.root, text="Start Time (sec):").pack()
        self.start_time = tk.Entry(self.root, width=10)
        self.start_time.pack(pady=5)
        
        tk.Label(self.root, text="End Time (sec):").pack()
        self.end_time = tk.Entry(self.root, width=10)
        self.end_time.pack(pady=5)

        # Add Subtitle Button
        self.add_button = tk.Button(self.root, text="Add Subtitle", command=self.add_subtitle)
        self.add_button.pack(pady=10)
        
        # Export SRT Button
        self.export_button = tk.Button(self.root, text="Export SRT", command=self.export_srt)
        self.export_button.pack(pady=10)

    def load_video(self):
        self.video_path = filedialog.askopenfilename(title="Select Video File",
                                                     filetypes=(("MP4 files", "*.mp4"), ("All files", "*.*")))
        if self.video_path:
            self.video = mp.VideoFileClip(self.video_path)
            messagebox.showinfo("Info", f"Video loaded: {self.video_path}")

    def add_subtitle(self):
        if not self.video_path:
            messagebox.showerror("Error", "Load a video first.")
            return
        
        try:
            start = float(self.start_time.get())
            end = float(self.end_time.get())
            text = self.subtitle_text.get()
            
            if start >= end:
                raise ValueError("Start time must be before end time.")
            
            # Create a subtitle entry
            subtitle = pysrt.SubRipItem(index=len(self.subtitles) + 1,
                                        start=pysrt.SubRipTime.from_seconds(start),
                                        end=pysrt.SubRipTime.from_seconds(end),
                                        text=text)
            self.subtitles.append(subtitle)
            messagebox.showinfo("Info", "Subtitle added.")
            self.subtitle_text.delete(0, tk.END)
            self.start_time.delete(0, tk.END)
            self.end_time.delete(0, tk.END)
        except ValueError as e:
            messagebox.showerror("Error", str(e))

    def export_srt(self):
        if not self.subtitles:
            messagebox.showerror("Error", "No subtitles to export.")
            return
        
        srt_path = filedialog.asksaveasfilename(defaultextension=".srt", filetypes=(("SRT files", "*.srt"),))
        if srt_path:
            self.subtitles.save(srt_path, encoding="utf-8")
            messagebox.showinfo("Info", f"SRT file saved: {srt_path}")

# Run the app
root = tk.Tk()
app = SubtitleMaker(root)
root.mainloop()
