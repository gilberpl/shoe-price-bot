# gilberpl
# shoe-price-bot


> This program searches two websites for the price of shoes using selenium. After grabbing the price of the shoes using HTML path, it will send a text to the users phone using twilio. Lastly, the program will send a notification if either websites price drops to $60.00 or below. This notification includes a link to the shoes so that they can be immediately purchased. 

* The links where gathered manually by finding the same shoe on each website
* There are stronger ways to get the price from the HTML this was my method

# GIF Example

![GIF](https://github.com/me50/gilberpl/blob/gilberpl-readme/WebScraper.gif)

## Example Code

```python

from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.chrome.options import Options
chrome_options = Options()
chrome_options.add_argument('--headless')
chrome_options.add_argument('--log-level=3')
import time
from twilio.rest import Client

def turnFloat(price):
    price = price.strip('$')
    price = float(price)
    return price
    
#Open the browser
driver = webdriver.Chrome()

#Giving the driver a URL for Zappos
zapposURL = "https://www.zappos.com/p/nike-revolution-5-cool-grey-pure-platinum-dark-grey/product/9266704/color/591040"
driver.get(zapposURL)


#Using the class to locate price in HTML which can change time and time again
shoePrice = driver.find_elements_by_xpath("//span[@class='SH']")

zapposPriceNum = 100.00
#Find the price in HTML
for nikePrice in shoePrice:
    if nikePrice.text:
        print("\nThe shoe price for Zappos:", nikePrice.text)
        #zapposPrice = nikePrice.text
        zapposPriceNum = turnFloat(nikePrice.text)

#print("\nI cant tell did it work?  ", zapposPriceNum)


#Giving the driver a URL for Amazon
amazonURL = "https://www.amazon.com/Nike-Revolution-Running-Platinum-Dark-Regular/dp/B07NM2MWGZ/ref=asc_df_B07NM2MWGZ/?tag=hyprod-20&linkCode=df0&hvadid=397083508730&hvpos=&hvnetw=g&hvrand=13124197771639347843&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9008422&hvtargid=pla-838924738026&psc=1&language=en_US&tag=&ref=&adgrpid=84837450398&hvpone=&hvptwo=&hvadid=397083508730&hvpos=&hvnetw=g&hvrand=13124197771639347843&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9008422&hvtargid=pla-838924738026"
driver.get(amazonURL)

#<span id="priceblock_ourprice" class="a-size-medium a-color-price priceBlockBuyingPriceString">$70.79</span>
aShoePrice = driver.find_elements_by_xpath("//span[@class='a-size-medium a-color-price priceBlockBuyingPriceString']")

amazonPriceNum = 100.00

for nikePrice in aShoePrice:
    if nikePrice.text:
        print("\nThe shoe price for Amazon:", nikePrice.text)
        amazonPriceNum = turnFloat(nikePrice.text)

smsText = ""

#Insert your own personal inputs here
AccountSid =  "S894k39495n485s";
APIKey = "S894k39495n485s";
APISecret  = "S894k39495n485s"; 

client = Client(APIKey, APISecret, AccountSid);

def sendMessage():
    message = client.messages \
                    .create(
                        body=smsText,
                        from_='+180099999',
                         to='+180099998'
                    )

#Setting thresholds for sending a text notification
if zapposPriceNum < 60.01:
    smsText = "Zappos Price: " + str(zapposPriceNum) + "  " + zapposURL
    sendMessage()

if amazonPriceNum < 60.01:
    smsText = "Amazon Price: " + str(amazonPriceNum) + "  " + amazonURL
    sendMessage()
