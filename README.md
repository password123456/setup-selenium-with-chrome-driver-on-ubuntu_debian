# Setup selenium with chrome driver on ubuntu/debian
[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Fpassword123456%2Fsetup_selenium_with_chrome_driver_on_ubuntu_debian&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

- Setting up and running Chrome and Selenium on the ubuntu or debian
- The guide is based on ubuntu 22.04
```
# cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=22.04
DISTRIB_CODENAME=jammy
DISTRIB_DESCRIPTION="Ubuntu 22.04.3 LTS"

confirmed at 2023.10.04
```

## update the all packages, if necessary
```
# apt update
# apt upgrade
```

## download chrome stable package
```bash
# wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

## Install google-chrome
```bash
# dpkg -i google-chrome-stable_current_amd64.deb
```

## check installed chrome version
```bash
# google-chrome --version
Google Chrome 117.0.5938.132
```

## Install selenium, webdriver-manager
- https://pypi.org/project/webdriver-manager/

```bash
# pip3 install selenium
# pip3 install webdriver-manager
```

## create hello_world
1) Run with Chrome Headless
```bash
# vim test.py
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

options = Options()
options.add_argument('--headless')
options.add_argument('--no-sandbox')
options.add_argument('--disable-dev-shm-usage')
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=options)

driver.get("https://python.org")
print(driver.title)
driver.close()
```

2) Run with Chrome GUI
```bash
# vim test.py
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

options = Options()
# options.add_argument('--headless')
# options.add_argument('--no-sandbox')
options.add_argument('--disable-dev-shm-usage')
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=options)

driver.get("https://python.org")
print(driver.title)
driver.close()
``` 

# run test.py and check chrome is available
```
# python test.py
Welcome to Python.org
```

# test's all/ enjoy it

## FAQ
### A.My VM is running in a cloud environment. In this case, how should I use Chrome?
- In a cloud environment, people generally use Chrome in headless mode.
- However, if your VM supports X-windows or display, you can also use GUI mode

### I wrote a Python script using Chrome and Selenium, and scheduled it with crontab, but cron is not working correctly. What should I do?
- many of search results it appears that you need to specify the display mode as 0 in your script when running it through cron.
- Here's an example:
```
*/1 * * * * export DISPLAY=:0; python ~/selenium_script.py
```
- However, typical VMs running Chrome execute in headless mode, where display settings are not relevant.
- The issue may be that the cron execution does not have the chromedriver in its path. You can specify the path to the chromedriver installed using dpkg.
- If you don't know the path to chromedriver, you can find it using the following command (or by setting WDM_LOG mode):
```
# find / -name "chromedriver"
/root/.cache/selenium/chromedriver
/root/.cache/selenium/chromedriver/linux64/117.0.5938.92/chromedriver
/root/.wdm/drivers/chromedriver
/root/.wdm/drivers/chromedriver/linux64/117.0.5938.92/chromedriver-linux64/chromedriver
```
- Afterward, you can set the path in crontab and execute it, like this:
```
*/05 * * * * export PATH=$PATH:/root/.wdm/drivers/chromedriver/linux64/117.0.5938.92/chromedriver-linux64/chromedriver; root /data/test.py
```
Or, you can create a Bash script that loads the Python script and specifies the path:
```
#!/bin/bash

export PATH=$PATH:/root/.wdm/drivers/chromedriver/linux64/117.0.5938.92/chromedriver-linux64/chromedriver
python3 test.py
```

