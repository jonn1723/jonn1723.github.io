# FlightHub Ticket Scraper
As someone who flies frequently, I decided to write a program that emails me the best offers in a set time interval. I noticed that email 
notifications don't always send the best offers, and that I would sometimes find better offers when I browse at various times. This project
was also to help consolidate the python fundamentals I recently learnt. The code is as follows:

---

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
import time
from selenium.webdriver.common.keys import Keys
import calendar
from selenium.common.exceptions import NoSuchElementException
import datetime
import pandas as pd
import smtplib
from email.message import EmailMessage
import ssl
import certifi

serv = Service('/usr/local/bin/chromedriver')
driver = webdriver.Chrome(service=serv)

# flight parameters
leaving_from = 'yyz'
going_to = 'yvr'
departure_date = "May 28, 2023"
results = 3
refresh_rate = 180  # minutes

driver.get("https://www.flighthub.com/")
driver.maximize_window()

# clicking on one way
oneway_xpath = "//a[@data-flight-type='one-way']"
oneway_tab = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.XPATH, oneway_xpath))
).click()
time.sleep(1)

# setting FROM location
from_destination_xpath = "//input[@name='seg0_from']"
from_tab = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.XPATH, from_destination_xpath))
)
from_tab.click()
from_tab.send_keys(leaving_from)
from_tab.send_keys(Keys.ENTER)
time.sleep(1)

# setting TO location
from_tab.send_keys(Keys.TAB)
to_destination_xpath = "//input[@name='seg0_to']"
to_tab = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.XPATH, to_destination_xpath))
)
to_tab.send_keys(going_to)
to_tab.send_keys(Keys.ENTER)
time.sleep(1)

# choosing departure date
to_tab.send_keys(Keys.TAB)
time.sleep(1)
departure_month = departure_date[0:3]
# month minus 1 cuz their coding sucks
abbr_to_num = {name: num for num, name in enumerate(calendar.month_abbr) if num}
departure_month_num = (abbr_to_num[departure_month])
flighthub_month = (abbr_to_num[departure_month]) - 1
flighthub_year = int(departure_date[8:13])
# finding the month on the flighthub interactive calendar
month_xpath = '//td[contains(@data-month,"{}")]'.format(flighthub_month)
departing_sub = ""
while departing_sub == "":
    try:
        driver.find_element(By.XPATH, month_xpath)
        break
    except NoSuchElementException:
        next_month_xpath = '/html[1]/body[1]/div[3]/div[1]/div[2]/div[1]/a[1]'
        next_month_tab = WebDriverWait(driver, 20).until(
            EC.presence_of_element_located((By.XPATH, next_month_xpath))
        )
        next_month_tab.click()
        time.sleep(1)
    continue
time.sleep(1)

# getting this month
current_time = datetime.datetime.now()
current_month = current_time.month
current_date = current_time.day

driver.execute_script("window.scrollTo(0, 200)")
time.sleep(3)


# departure month - current month
def month_difference(a, b):
    month_result = a - b
    return month_result


month_difference(departure_month_num, current_month)

# getting days
departure_month_date = int(departure_date[3:6])
if month_difference(departure_month_num, current_month) >= 2:
    previous_month_days = calendar.monthrange(flighthub_year, flighthub_month)[1]
    added_days = departure_month_date + previous_month_days
    date_xpath = "(//a[@href='#'])[{}]".format(added_days)
    date_tab = WebDriverWait(driver, 20).until(
        EC.presence_of_element_located((By.XPATH, date_xpath))
    )
    date_tab.click()
    time.sleep(5)
elif month_difference(departure_month_num, current_month) == 1:
    days_in_this_month = calendar.monthrange(flighthub_year, flighthub_month)[1]
    days_left_in_month = days_in_this_month - current_date + 1
    days_til_fight = days_left_in_month + departure_month_date
    date_xpath = "(//a[@href='#'])[{}]".format(days_til_fight)
    date_tab = WebDriverWait(driver, 20).until(
        EC.presence_of_element_located((By.XPATH, date_xpath))
    )
    date_tab.click()
    time.sleep(5)
else:
    days_to_departure = departure_month_date - current_date + 1
    date_xpath = "(//a[@href='#'])[{}]".format(days_to_departure)
    date_tab = WebDriverWait(driver, 20).until(
        EC.presence_of_element_located((By.XPATH, date_xpath))
    )
    date_tab.click()
    time.sleep(5)

# clicking search
search_xpath = "//a[@id='btn-search-flight']"
search_tab = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.XPATH, search_xpath))
).click()
time.sleep(5)

# scrolling down
driver.execute_script("window.scrollTo(0, 600)")
time.sleep(3)

# checking for non-stop flights w/o alternate airports
one_stop_xpath = "//label[@for='1']"
one_stop_tab = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.XPATH, one_stop_xpath))
).click()
time.sleep(1)

two_stop_sub = ""
while two_stop_sub == "":
    try:
        two_stop_xpath = "//label[@for='2']"
        two_stop_tab = WebDriverWait(driver, 20).until(
            EC.presence_of_element_located((By.XPATH, two_stop_xpath))
        ).click()
        time.sleep(1)
        break
    except NoSuchElementException:
        continue

alt_days_xpath = "//label[@for='qual-show-alternate-airports']"
alt_days_tab = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.XPATH, alt_days_xpath))
).click()
time.sleep(1)

# scraping data
lowest_tickets = []
lowest_tickets_airline = []
departure_time = []
arrival_time = []
results_shown = results + 1

for t in range(1, results_shown):
    tickets = driver.find_elements(By.XPATH, "//div//span[@class='total-price']")
    lowest_tickets.append(tickets[t].text)

for t in range(1, results_shown):
    airline = driver.find_elements(By.XPATH, "//div[@class='ellipsis-wrap']")
    lowest_tickets_airline.append(airline[t].text)

for d in range(1, results_shown):
    ticket_departure_xpath = '//*[@id="fares-search-package-list"]/div/ul/li[' + str(
        d) + ']/div[3]/div[1]/div[1]/div[2]/ul[1]/li[1]'
    departure_time.append(driver.find_element(By.XPATH, ticket_departure_xpath).text)

for ar in range(1, results_shown):
    ticket_arrival_xpath = '//*[@id="fares-search-package-list"]/div/ul/li[' + str(
        ar) + ']/div[3]/div[1]/div[1]/div[2]/ul[2]/li[1]'
    arrival_time.append(driver.find_element(By.XPATH, ticket_arrival_xpath).text)

z = zip(lowest_tickets, lowest_tickets_airline, departure_time, arrival_time)
mapped = list(z)
df = pd.DataFrame(mapped, columns=['Price', 'Airline', 'Departure Time', 'Arrival Time'])


# emailing to self
def send_email():
    email_sender = 'duhmisterchong@gmail.com'
    email_receiver = 'duhmisterchong@gmail.com'
    email_password = 'ciiuohwfqcnqrgoa'
    msg = EmailMessage()

    msg['From'] = email_sender
    msg['To'] = email_receiver

    msg['Subject'] = "Cheapest flights from {} to {}, ".format(leaving_from, going_to)

    msg.add_alternative('''\
                <!DOCTYPE html>
                <html>
                    <body>
                        {}
                    </body>
                </html>'''.format(df.to_html()), subtype="html")

    context = ssl.create_default_context(cafile=certifi.where())
    with smtplib.SMTP_SSL('smtp.gmail.com', 465, context=context) as smtp:
        smtp.login(email_sender, email_password)
        smtp.sendmail(email_sender, email_receiver, msg.as_string())
    driver.refresh()


start_time = time.time()
while True:
    send_email()
    time.sleep(refresh_rate - ((time.time() - start_time) % refresh_rate))
```
