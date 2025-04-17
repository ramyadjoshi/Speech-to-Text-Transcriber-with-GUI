# Speech-to-Text-Transcriber-with-GUI
This is a Python-based speech-to-text transcriber application with a user-friendly GUI built using Tkinter. It allows users to record their voice via microphone, convert speech to text using Google’s Speech Recognition API, and view or save the transcribed text. The application is lightweight, beginner-friendly.

#Import the necessary libraries 
import tkinter as tk
from tkinter import messagebox, scrolledtext
import speech_recognition as sr

# Create the main application window
class SpeechToTextApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Speech to Text Transcriber")
        self.root.geometry("500x400")
        self.root.resizable(False, False)

        # Title label
        self.label = tk.Label(root, text="🎤 Speech to Text", font=("Helvetica", 18, "bold"))
        self.label.pack(pady=10)

        # Transcription display box
        self.text_area = scrolledtext.ScrolledText(root, wrap=tk.WORD, font=("Helvetica", 12), height=10, width=50)
        self.text_area.pack(pady=10)

        # Record button
        self.record_button = tk.Button(root, text="Start Recording", command=self.transcribe_audio, font=("Helvetica", 12), bg="green", fg="white")
        self.record_button.pack(pady=5)

        # Save button (optional)
        self.save_button = tk.Button(root, text="Save Transcription", command=self.save_text, font=("Helvetica", 12), bg="blue", fg="white")
        self.save_button.pack(pady=5)

    def transcribe_audio(self):
        recognizer = sr.Recognizer()

        with sr.Microphone() as source:
            try:
                self.text_area.delete(1.0, tk.END)
                self.text_area.insert(tk.END, "Listening...\n")
                self.root.update()

                # Adjusting for ambient noise and setting dynamic energy threshold
                recognizer.adjust_for_ambient_noise(source, duration=1)  # Adjust for 1 second of ambient noise
                self.text_area.insert(tk.END, "Listening for speech...\n")
                self.root.update()

                # Increased duration of listening
                audio = recognizer.listen(source, timeout=None)  # Listen indefinitely until speech ends

                self.text_area.insert(tk.END, "Transcribing...\n")
                self.root.update()

                # Recognize speech using Google Speech Recognition
                text = recognizer.recognize_google(audio)
                self.text_area.delete(1.0, tk.END)
                self.text_area.insert(tk.END, text)

            except sr.WaitTimeoutError:
                messagebox.showerror("Timeout", "No speech detected. Try again.")
            except sr.UnknownValueError:
                messagebox.showerror("Error", "Sorry, could not understand the audio.")
            except sr.RequestError as e:
                messagebox.showerror("API Error", f"Could not request results; {e}")

    def save_text(self):
        text = self.text_area.get(1.0, tk.END).strip()
        if text:
            try:
                with open("transcription.txt", "w", encoding='utf-8') as file:
                    file.write(text)
                messagebox.showinfo("Saved", "Transcription saved as 'transcription.txt'")
            except Exception as e:
                messagebox.showerror("Save Error", f"Failed to save file: {e}")
        else:
            messagebox.showwarning("No Text", "There's nothing to save!")

# Run the app
if __name__ == "__main__":
    root = tk.Tk()
    app = SpeechToTextApp(root)
    root.mainloop()
