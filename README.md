# Google-Gemini-Scraping
Use Google Gemini API to scrap web dynamic web pages.

I tried scraping a dynamic webpage using Python's Selenium library. I wasn't able to understand how to use it ot screap dynamic content that does not show when inspecting the html code.

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

[matias@instance-20240318-165854-gemini ~]$ python --version
Python 3.9.18

Python 3.9 or greater is enough.









