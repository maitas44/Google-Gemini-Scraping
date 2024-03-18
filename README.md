# Google-Gemini-Scraping
Use Google Gemini API to scrap web dynamic web pages
I tried scraping a dynamic webpage using Python's Selenium library. I wasn't able to understand how to use it ot screap dynamic content that does not show when inspecting the html code.
I decided to create a Linux VM, install a X11 UI, and a web browser with a pugling that reloads the page each 5 minutes.
Then I created:
1- A python script that captures the complete image and saves it to a jg file each 5 minutes using cron.
2- A python script that sends the jpg to the Google Gemini API and checks if the needed text is shown. If that text is shown, it writes a text file with the text "It is happening". It runs each 5 minutes using cron.
2- A python script that checks if the previous file shows the text "It is happening", if it des sends me a Telegram message. It runs each 5 minutes using cron.
