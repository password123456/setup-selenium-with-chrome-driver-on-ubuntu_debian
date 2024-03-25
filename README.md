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


## Step 1: update the all packages, if necessary
```
# apt update
# apt upgrade
```

## Step 2: download chrome stable package
```bash
# wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

## Step 3: Install google-chrome
```bash
# dpkg -i google-chrome-stable_current_amd64.deb
```

## (Optional) If you have a dependency problem after step 3, running the following command then try again step 3
```bash
# sudo apt -f install
```

## Step 4: check installed chrome version
```bash
# google-chrome --version
Google Chrome 117.0.5938.132
```

## Step 5: Install selenium, webdriver-manager
- https://pypi.org/project/webdriver-manager/

```bash
# pip3 install selenium
# pip3 install webdriver-manager
```

## Step 6: create hello_world
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

# Step 7: run test.py and check chrome is available
```
# python3 test.py
Welcome to Python.org
```

# Step 8: that's all. enjoy it.:)

# FAQ :)
## A. My VM is running in a cloud environment. In this case, how should I use Chrome?
- In a cloud environment, people usually use Chrome in headless mode.
- In other words, in a cloud environment, VMs may not support display mode, especially free VMs(free tiers).
- However, if your VMs supports X-windows or display, you can also use GUI mode

## B. I wrote a Python script using Chrome and Selenium, and scheduled it with crontab, but cron is not working correctly. What should I do?
- many of search results it appears that you need to specify the display mode as 0 in your script when running it through cron.
- Like this:)
```
*/1 * * * * export DISPLAY=:0; python ~/selenium_script.py
```
- However, typical VMs running Chrome execute in headless mode, where display settings are not relevant.
- The issue may be that when execute cronjob cron does not have the chromedriver path in cron's path.
- so you have to specify the chromedriver path in cron or your launcher(script).
- If you don't know the path to chromedriver, you can find it using the following command (or by setting WDM_LOG mode):
```
# find / -name "chromedriver"
/root/.cache/selenium/chromedriver
/root/.cache/selenium/chromedriver/linux64/117.0.5938.92/chromedriver
/root/.wdm/drivers/chromedriver
/root/.wdm/drivers/chromedriver/linux64/117.0.5938.92/chromedriver-linux64/chromedriver
```
- Afterward, you can set the path in crontab and execute it, like this:)
```
*/05 * * * * export PATH=$PATH:/root/.wdm/drivers/chromedriver/linux64/117.0.5938.92/chromedriver-linux64/chromedriver; root /data/test.py
```
Or, you can create a Bash script that loads the Python script and specifies the path:
```
# vim test_loader.sh

#!/bin/bash

export PATH=$PATH:/root/.wdm/drivers/chromedriver/linux64/117.0.5938.92/chromedriver-linux64/chromedriver
...
...
python3 test.py
```

## C. The reason for the different ways of loading chromedriver is as follows, for example, what are the differences between (1) and (2)?
```
(1)
from selenium import webdriver
driver = webdriver.Chrome('/home/user/drivers/chromedriver')


(2)
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()))
```
- (1) Loads chromedriver using Selenium's webdriver. In this case, You need to download the chromedriver binary, unzip it somewhere on your PC and set the path to this driver.(Compatible with Selenium 4.x and below.)
- (2) Runs Chrome using webdriver-manager. When you use webdriver-manager package, there is no need to download the chromedriver binary separately.

Install manager:
```
pip install webdriver-manager
```
Use with Chrome
### selenium 3
```
from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(ChromeDriverManager().install())
```

### selenium 4
```
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()))
```
- For more details, refer to https://pypi.org/project/webdriver-manager/.

