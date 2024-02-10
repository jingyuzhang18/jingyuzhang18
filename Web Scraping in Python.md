# Web Scraping in Python

## Secret Links

Fill in the blanks below to assign an XPath string to the variable `xpath` which directs to all `href` attribute values of the hyperlink `a` elements whose class attributes contain the string `"package-snippet"`.

```python
xpath = '//a[contains(@class,"package-snippet")]/@href'
```

## XPath Chaining

`Selector` and `SelectorList` objects allow for *chaining* when using the `xpath` method. What this means is that you can apply the `xpath` method over once you've already applied it. For example, if `sel` is the name of our `Selector`, then

```
sel.xpath('/html/body/div[2]')
```

is the same as

```
sel.xpath('/html').xpath('./body/div[2]')
```

or is the same as

```
sel.xpath('/html').xpath('./body').xpath('./div[2]')
```

The only catch is that you need to "glue together" the XPath pieces by using a period at the start of each subsequent XPath string (notice the periods we added to the XPath strings in our examples).

```python
from scrapy import Selector

# Create a Selector selecting html as the HTML document
sel = Selector( text = html )

# Create a SelectorList of all div elements in the HTML document
divs = sel.xpath( '//div' )
```



### HTML text to Selector

```python
from scrapy import Selector 
import requests 
ur1= 'https://en.wikipedia.org/wiki/Web_scraping' 
html = requests.get( ur1 ).content 
sel = Selector( text = html )
```

```python
# Import a scrapy Selector
from scrapy import Selector

# Import requests
import requests

# Create the string html containing the HTML source
html = requests.get( url ).content

# Create the Selector object sel from html
sel = Selector( text = html )

# Print out the number of elements in the HTML document
print( "There are 1020 elements in the HTML document.")
print( "You have found: ", len( sel.xpath('//*') ) )
```

## CSS Locators, Chaining, and Responses

XPATH 

`xpath = '/html/body/ /div/p[2]`

CSS

`CSS = 'html > body div > p:nth-of-type(2)'`

In the second part of this problem, we want you to create a CSS Locator string which will select a certain collection of elements as described here: Select the hyperlink (`a` element) children of all `div` elements belonging to the class `"course-block"` (that is, any `div` element with a class attribute such that `"course-block"` is one of the classes assigned). The number of such elements is 11, so you can check your solution with `how_many_elements` if you choose.

`css_locator = 'div.course-block > a'`



```python
# Selecting all href attributes chaining with css
hrefs_from_css = course_as.css( '::attr(href)' )

# Selecting all href attributes chaining with xpath
hrefs_from_xpath = course_as.xpath( './@href' )
```



Assign to the variable `xpath` an XPath string directing to the text within the paragraph `p` element with `id` equal to `p3`, which **does not include** the text of future generations of this `p` element.

```python
# Create an XPath string to the desired text.
xpath = '//p[@id="p3"]/text()'

# Create a CSS Locator string to the desired text.
css_locator = 'p#p3::text'
```

```python
# Get the URL to the website loaded in response
this_url = response.url

# Get the title of the website loaded in response
this_title=response.xpath('/html/head/title/text()').extract_first()

# Print out our findings
print_url_title( this_url, this_title )

# Create a CSS Locator string to the desired hyperlink elements
css_locator = 'a.course-block__link'

# Select the hyperlink elements from response and sel
response_as = response.css( css_locator )
sel_as = sel.css( css_locator )

# Examine similarity
nr = len( response_as )
ns = len( sel_as )
for i in range( min(nr, ns, 2) ):
  print( "Element %d from response: %s" % (i+1, response_as[i]) )
  print( "Element %d from sel: %s" % (i+1, sel_as[i]) )
  print( "" )
  
# Select all desired div elements
divs = response.css('div.course-block')

# Take the first div element
first_div = divs[0]

# Extract the text from the (only) h4 element in first_div
h4_text = first_div.css('h4::text').extract_first()

# Print out the text
print( "The text from the h4 element is:", h4_text )
```



Similar to the work given in the previous lesson, we will have you use a pre-loaded `Response` object, named `response` to scrape the course titles from the (shortened version of the) DataCamp course directory https://www.datacamp.com/courses/all. To successfully do so, you only need to know the following

- The course titles **are the text** from all the `h4` elements within the HTML document.

```python
# Create a SelectorList of the course titles
crs_title_els = response.css( 'h4::text' )

# Extract the course titles 
crs_titles = crs_title_els.extract()

# Print out the course titles 
for el in crs_titles:
  print( ">>", el )
  
# Calculate the number of children of the mystery element
how_many_kids = len( mystery.xpath( './*' ) )
```



## Your Spider

1. Required imports

```python
import scrapy
from scrapy.crawler import CrawlerProcess
```

2. The part we will focus on: the actual spider

```python
class SpiderCassName(scrapy.Spider):
  name = "spider_name"
  # the code for your spider
```

3. Running the spider

```python
# initiate a CrawlerProcess
process = CrawlerProcess()
# tell the process which spider to use
process.crawl(YourSpider)
# start the crawling process
process.start()
```

### Weaving the Web

We now narrow in on creating the actual spider. This is the class object which basically tells us what sites we want to scrape and how we want to scrape them. Again, the code may look a little complicated, but we will walk through it and see that we've built up the technique to cover most of the work. Here we have a code to scrape the DataCamp course directory. It includes all the basic pieces we need. We can name the class anything we want, here we called the class "DCspider" and defined the name variable within that class with a similar name (although we can assign any string to the name variable we want). This name variable is important for some of the action that happens under the hood of scrapy. We must have a start_requests method to define which site or sites we want to scrape, and which tells us where to send the information from these sites to be parsed. Finally, we need to have at least one method to parse to the website we scrape; we can call the parsing method (or methods) anything we want as long as we correctly identify the method within the start_requests function. All we are doing in this parser (which we named parse) is taking the HTML code and writing it to a file.

```python
class DCspider( scrapy.Spider ):
  
  name = 'dc_spider'
	
  def start_requests( self ):
		urls = [ 'https://www.datacamp.com/courses/all' ]
		for url in urls:
			yield scrapy.Request( ur1 = url, callback = self.parse )
	
  def parse( self, response ):
		# simple example: write out the html
		html_file = 'DC_courses.html'
		with open( html_file, 'wb' ) as fout:
			fout.write( response.body )
```

As mentioned in the lesson, a `class` is roughly a collection of related variables and functions housed together. Sometimes one class likes to use methods from another class, and so we will *inherit* methods from a different class. That's what we do in the spider class.

### The Skinny on start_requests

First, within the spider class, we must define a method called start_requests which should take self as an input. The reason we don't have flexibility in this method name is because scrapy looks for the start_requests method by name within the class we define for our spider. We then have a list of the url or urls we would like to start scraping (but in this case, only one). It doesn't matter that we named this list urls, but it seems like a convenient name considering. Finally, we will take each url within the urls list and send it off to be dealt with. Now, this "send-off" is the most complicated part! But even before worrying about the "send-off" let's notice that we really did not need to loop over the urls list with only one url. We could have easily defined it without the for loop, but we wrote it originally with the loop to give you an idea of how you might construct this if you had several urls you want to initiate the spider with. Back to the "send-off": the yield command, which you might be familiar with already, acts kind of like a return command in that it returns values when the start_requests is run; yield is a python call, and is not specific to scrapy. We won't go into more detail about yield vs return here, but just note we are using yield in this method. The object we are yielding is a scrapy.Request object. We haven't seen these before, but again, that's OK. What yielding the scrapy.Request object does is send a response variable (the same response variable we are familiar with) pre-loaded with the HTML code from the url argument of the scrapy.Request call, to the parsing function defined in the callback argument

### Zoom Out

So, if we look at the entire spider class again, what we see is that the start_request call will pre-load a response variable with the HTML code from the DataCamp course directory, and send it to the method we have named parse. As a glimpse into the future, notice that the parse method has response as its second input variable, this is the variable passed from the scrapy.Request call. Also, notice that while there are many wheels turning in the start_requests method, most of them are happening under the hood, and the only real adjustments we need to make are to define which url or urls we are going to scrape, and what callback method we want to use to parse those scraped sites.

### Self Referencing is Classy

You probably have noticed that within the spider class, we always input the argument `self` in the `start_requests` and `parse` methods (just look in the sample code in this exercise!). This allows us to reference between methods within the class. That is, if we want to refer to the method `parse` within the `start_requests` method, we would need to write `self.parse` rather than just `parse`; what writing `self` does is tell the code: "Look in the same class as `start_requests` for a method called `parse` to use."

```python
# Create the spider class
class YourSpider( scrapy.Spider ):
  name = "your_spider"
  # start_requests method
  def start_requests( self ):
    self.print_msg( "Hello World!" )
  # parse method
  def parse( self, response ):
    pass
  # print_msg method
  def print_msg( self, msg ):
    print( "Calling start_requests in YourSpider prints out:", msg )
```

### Saves these links to a file with one link per line.

```python
class DCspider( scrapy.Spider ):
  
  name = 'dc_spider'
	
  def start_requests( self ):
		urls = [ 'https://www.datacamp.com/courses/all' ]
		for url in urls:
			yield scrapy.Request( ur1 = url, callback = self.parse )
	  
	def parse( self, response ):
		links = response.css('div.courseblock>a::attr(href)').extract()
    filepath = 'DC_links.csv'
    with open( filepath, 'w')as f:
      f.writelines( [link + '/n' for link in links] )
```

### create a spider which crawls between different sites

We start by first extracting the course links from the DataCamp course directory (as we did in the last chapter). Then, instead of printing anything to a file here, we will have the spider follow those links and parse those sites in a second parser. You see, finishing the first parse method, we loop over the links we extracted from the course directory, then we send the spider to follow each of those links and scrape those sites with the method <u>parse2</u>. Notice here that when we send our spider from the first parser to the second, we again use a yield command. **But, instead of creating a `scrapy.Request` call (like we did in start_requests), we use the `.follow` method in the response variable itself. The `.follow` method works similarly to the `scrapy.Request` call, where we need to input the url we want to use to load a response variable and use the callback argument to point the spider to which parsing method we are going to use next.**

```python
class DCspider( scrapy.Spider ):
  
  name = 'dc_spider'
	
  def start_requests( self ):
		urls = [ 'https://www.datacamp.com/courses/all' ]
		for url in urls:
			yield scrapy.Request( ur1 = url, callback = self.parse )

	def parse( self, response ):
    links =response.css('div.course-block > a::attr(href)').extract()
    for link in links:
      yield response.follow( url = link, callback = self.parse2)
	
  def parse2( self, response):
    # parse the course sites here!
```

```python
# Import the scrapy library
import scrapy

# Create the Spider class
class DCdescr( scrapy.Spider ):
  name = 'dcdescr'
  # start_requests method
  def start_requests( self ):
    yield scrapy.Request( url = url_short, callback = self.parse )
  
  # First parse method
  def parse( self, response ):
    links = response.css( 'div.course-block > a::attr(href)' ).extract()
    # Follow each of the extracted links
    for link in links:
      yield response.follow(url = link, callback = self.parse_descr)
      
  # Second parsing method
  def parse_descr( self, response ):
    # Extract course description
    course_descr = response.css( 'p.course__description::text' ).extract_first()
    # For now, just yield the course description
    yield course_descr
```



```python
# Import scrapy
import scrapy

# Import the CrawlerProcess: for running the spider
from scrapy.crawler import CrawlerProcess

# Create the Spider class
class DC_Chapter_Spider(scrapy.Spider):
  name = "dc_chapter_spider"
  # start_requests method
  def start_requests(self):
    yield scrapy.Request(url = url_short,
                         callback = self.parse_front)
  # First parsing method
  def parse_front(self, response):
    course_blocks = response.css('div.course-block')
    course_links = course_blocks.xpath('./a/@href')
    links_to_follow = course_links.extract()
    for url in links_to_follow:
      yield response.follow(url = url,
                            callback = self.parse_pages)
  # Second parsing method
  def parse_pages(self, response):
    crs_title = response.xpath('//h1[contains(@class,"title")]/text()')
    crs_title_ext = crs_title.extract_first().strip()
    ch_titles = response.css('h4.chapter__title::text')
    ch_titles_ext = [t.strip() for t in ch_titles.extract()]
    dc_dict[ crs_title_ext ] = ch_titles_ext

# Initialize the dictionary **outside** of the Spider class
dc_dict = dict()

# Run the Spider
process = CrawlerProcess()
process.crawl(DC_Chapter_Spider)
process.start()

# Print a preview of courses
previewCourses(dc_dict)
```

