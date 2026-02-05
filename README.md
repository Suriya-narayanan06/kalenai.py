import speech_recognition as sr
import pyttsx3
import wikipediaapi
import webbrowser
import datetime

engine = pyttsx3.init()
engine.setProperty('rate', 170)

def speak(text):
    engine.say(text)
    engine.runAndWait()

def wishMe():
    hour = datetime.datetime.now().hour
    if 0 <= hour < 12:
        speak("Good morning")
    elif 12 <= hour < 18:
        speak("Good afternoon")
    else:
        speak("Good evening")
    speak("I am your AI assistant. How can I help you?")

def takeCommand():
    r = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening...")
        r.pause_threshold = 1
        audio = r.listen(source)
    try:
        print("Recognizing...")
        query = r.recognize_google(audio, language='en-in')
        print("User said:", query)
        return query.lower()
    except sr.UnknownValueError:
        speak("Sorry, I didn't catch that.")
        return "none"
    except sr.RequestError:
        speak("Network issue.")
        return "none"

def wiki_search(query):
    wiki = wikipediaapi.Wikipedia('en')
    page = wiki.page(query)
    if page.exists():
        speak("According to Wikipedia")
        speak(page.summary[:500])
    else:
        speak("No results found on Wikipedia")

def write_note():
    speak("What should I write?")
    note = takeCommand()
    with open("notes.txt", "a") as f:
        f.write(note + "\n")
    speak("Note saved successfully")

def main():
    wishMe()
    while True:
        query = takeCommand()
        if query == "none":
            continue
        elif "open google" in query:
            speak("Opening Google")
            webbrowser.open("https://www.google.com")
        elif "open youtube" in query:
            speak("Opening YouTube")
            webbrowser.open("https://www.youtube.com")
        elif "wikipedia" in query:
            speak("What should I search?")
            topic = takeCommand()
            wiki_search(topic)
        elif "time" in query:
            current_time = datetime.datetime.now().strftime("%H:%M:%S")
            speak("The time is " + current_time)
        elif "write a note" in query:
            write_note()
        elif "exit" in query or "stop" in query:
            speak("Goodbye. Take care.")
            break
        else:
            speak("Command not recognized")

if _name_ == "_main_":
    main()
