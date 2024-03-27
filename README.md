# Google-Gemini-Screenshot
Use Google Gemini API to perform OCR on image captire of web dynamic web pages.

I tried looking for info on a dynamic webpage using Python's Selenium library. I wasn't able to understand how to use it ot screap dynamic content that does not show when inspecting the html code.

I decided to create a Linux VM, install a X11 UI, and a web browser with a pugling that reloads the page each 5 minutes.
Then I created:

1- A python script that captures the complete image and saves it to a jg file each 5 minutes using cron.

2- A python script that sends the jpg to the Google Gemini API and checks if the needed text is shown. If that text is shown, it writes a text file with the text "It is happening". It runs each 5 minutes using cron.

3- A python script that checks if the previous file shows the text "It is happening", if it des sends me a Telegram message. It runs each 5 minutes using cron.

How to set up the environment:

1- Create a Centos9 VM and install VNC server.

    sudo yum update

    sudo yum install tigervnc -y

    sudo yum install tigervnc-server -y

    sudo yum groupinstall "Server with GUI"  -y

    sudo systemctl stop firewalld

    sudo vncserver :1

// this step is to get a fresh xstartup file to edit. You need to set up a password.

    sudo vncserver -kill :1

    sudo vim /root/.vnc/xstartup

//edit /root/.vnc/xstartup and add "X11&" at the end as a new final line.

    sudo vncserver :1

Access your VNC instance using a VNC client. In my chromebook Linux enviornment I installed remina and start it with:

    /usr/bin/remmina

Once inside Centos9, open firefox. Install refresh plugin.

![image](https://github.com/maitas44/Google-Gemini-Scraping/assets/46689794/85a1eb37-31c2-4ef2-b8ab-cf9976cd4a7d)

![image](https://github.com/maitas44/Google-Gemini-Scraping/assets/46689794/94df6afe-5ceb-44bb-b86b-7c066b4c8fcf)

Check python version:

    python --version
    Python 3.9.18

Python 3.9 or greater is enough.

Create a python virtual environment

    python -m venv my_env

To enable the virtual environment:

    source my_env/bin/activate

To disable later on:

    (my_env) $ deactivate

Go to the directory

    cd my_env

Export display to the ssh session.

    export DISPLAY=:1

Install required library:

    pip install pyautogui

    sudo yum install -y python3-tkinter tk-devel 

Use an editor to create a capture.py file with the following data:

    import pyautogui
    import time
    while True:  # Loop runs forever
        try:
            # Take the screenshot
            my_screenshot = pyautogui.screenshot()
            # Generate a unique filename with a timestamp
            import datetime
            filename = "screenshot.jpg"
            # Save as a JPEG 
            my_screenshot.save(filename, quality=95)
        except Exception as e:
            print(f"An error occurred: {e}")  # Log any errors
        time.sleep(300)  # Wait 300 seconds (5 minutes)

keep it running on a terminal window:

    python capture.py

Open a new terminal and start the python virtual environment:

![image](https://github.com/maitas44/Google-Gemini-Scraping/assets/46689794/92cf9746-5957-4aee-93a1-cf7bf8e238c8)

run capture.py

    python capture.py

Use an editor to create a geminiocr.py file with the following data:

    GOOGLE_API_KEY = "AIzaSyASkqY3NBIsqyqSxEaW4ta-"
    import pathlib
    import textwrap
    import time  # Import the time library
    import google.generativeai as genai
    import PIL.Image
    from PIL import Image
    from IPython.display import display
    from IPython.display import Markdown
    def to_markdown(text):
      text = text.replace('•', ' *')
      return Markdown(textwrap.indent(text, '> ', predicate=lambda _: True))
    def process_image():
      model = genai.GenerativeModel('gemini-pro-vision')
      image = PIL.Image.open('/root/my_env/screenshot.jpg')
      response = model.generate_content(["Assume you are an OCR. answer with all the text you can detect in this image", image])
      detected_text = ""
      for output in response:
        if output.text:
          detected_text = output.text
          break  # Found text, stop iterating
      if "no hay turno" in detected_text:
        with open("salida.txt", "w") as f:
          f.write("no hay turno")
      else:
        with open("salida.txt", "w") as f:
          f.write("SI HAY TURNO")
    if __name__ == "__main__": 
        genai.configure(api_key=GOOGLE_API_KEY)  # Configure outside the loop 
        while True:
          process_image()
          time.sleep(300)  # Wait for 5 minutes (300 seconds)

replace GOOGLE_API_KEY = "AIzaSyASkqY3NBIsqyqSxEaW4ta-" with a valid API KEY from https://makersuite.google.com/

open a new terminal widnow , activate the python virtual environment and run

    python geminiocr.py

open a new terminal window.

open python virtual environment and Install telegram libary:

    pip install python-telegram-bot 
    pip install pyTelegramBotAPI

Get a Bot Token:

Open Telegram and search for "BotFather".

Start a conversation with BotFather and use the /newbot command.

Follow BotFather's instructions to give your bot a name and username.

BotFather will provide you with a bot token.

Find Your Chat ID:

Search for the "@get_id_bot" bot in Telegram.

Start a conversation with the bot, and it will send you a message containing your Chat ID.

Use an editor to create a telegram.py file with the following data:

    import telebot
    import time
    BOT_TOKEN = '7079927993:AAH2wqCV8pTepEXwbpCuHaW9lJ4'
    CHAT_ID = 70174
    bot = telebot.TeleBot(BOT_TOKEN)
    def check_and_send_message():
        with open("salida.txt", "r") as f:
            contents = f.read()
            if "SI HAY TURNO" in contents:
                bot.send_message(CHAT_ID, "Hay un turno disponible!")  
    if __name__ == "__main__":
        while True:
            check_and_send_message()
            time.sleep(300)  # Wait for 5 minutes 

replace BOT_TOEKN and CHAT_ID with your data. Run

    python telegram.py

Now you will receive a Telegram message if the string "no hay turno" is NOT detected in the screen.
