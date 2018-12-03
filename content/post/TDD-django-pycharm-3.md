+++

title = "Notes to Test driven development with Django using pycharm (chp 4-5)"

description = ""

tags = ["Python","Pycharm", "Django"]

categories = [ "My_note",   "Development" ]

date = "2016-07-09"

toc = true 

+++



## 0 Checkpoints

* Don't use unit test to test a constant. Unit test is about testing logic, flow control and configuration. 


* Run unit tests from pycharm IDE : Tools\=\=>Run manage.py task ...
* Another development cycle, follow the pattern, i.e. if you choose MCV, then if you changed M then you should change C, and V correspondingly.
* Organize the code in unit test: create test, call function under the test and assertions

## 1 Refactoring to use a Template

* We are going to use a separate html file instead of HttpResponse to pass the unit check.


* Refactoring is a specialize topic that you could refer to here:http://www.dummies.com/how-to/content/scrum-software-flexibility-and-refactoring.html

* But you may have had experience with the template used in MS Office/Word and that is a simple form of refactoring. So if we build a HTML file separately instead of writing HTML code as a string in python HttpResponse function, we could separate different languages. This idea itself is a good one: easier to manage and maintain later. And, it allows you to use HTML file repeatedly if needed, better than copy/paste code here and there inside the python.

* Under "lists" folder, we create "templates" folder then setup a "home.html"

  ```html
  lists/templates/home.html
  <html>
  	<title> To-Do lists </title>
  </html>
  ```

* Then we modify view.py (MVC)

  ```python
  list/views.py
  from django.shortcuts import render

  def home_page(request):
      return render(request, 'home.html')
  ```

  * render is another function we used from Django. it mainly takes request as first parameter and then looking for "home.html"(the second parameter) in folders called "templates" in app (The app needs to be registered in settings.py). 

* Then we go ahead to register our list app in settings.py:

  ```
  superlists/settings.py
  # Application definition

  INSTALLED_APPS = (
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
      'lists',
  )
  ```

  ​

* Run the unit tests. This time we use pycharm's built-in menu. From "Tools\=\=> Run manage.py task..." there will be a new tab open in log window.

![tdd-django-pycharm-3-1](/images/tdd-django-pycharm-3-1.png)



## 2 Testing more than the constant

* We then add more serious unit tests. The will check whether we are rendering the right template file. We add more code to lists/tests.py

  ```python
  lists/tests.py
  from django.template.loader import render_to_string
  [...]

      def test_home_page_returns_correct_html(self):
          request = HttpRequest()
          response = home_page(request)
          expected_html = render_to_string('home.html')
          self.assertEqual(response.content.decode(), expected_html)
  ```

  * The ==purpose== of this new test is more or less be stated by the function's name. We want to know whether the "render" returns the right html file. The reason is there maybe several files of the same names, so render may return a wrong file. The ==method== is simple, we compare the result from HttpResponse and result from render.
  * render_to_string change the HTML code to normal python string, then we could compare that with response.content.decode(), which also convert response back to python string.

## 3 More code on the html file

* We first add more code to the html file. Of course, according to TDD (test-driven development), we should, theoretically write the test first. However, since this is my note, I follow the user story first----so I wouldn't pretend to follow that test procedure. However, if you are new, you should follow the book.

```html
<body>
    <h1>Your To-Do list</h1>
    <form method="POST">
        <input name="item_text" id="id_new_item" placeholder="Enter a to-do item" />
        {% csrf_token %}
    </form>

    <table id="id_list_table">
        <tr><td>{{ new_item_text }}</td></tr>
    </table>
</body>
```

* As we said before, this is going to be a To-Do list. And the simple user story from the functional test is the you should be able to create a table and then choose an item from the list to create a to-do. This is a first step for that.

* Some explanations: {% csrf_token %} is for csrf token and {{...}} is for html runtime template, which means during the running, some new code will replace the {{..}} . And you can simple think them like place holder for some more functions.

  Remember, we change the M (MVC pattern), so we should change V to reflect the changes. So you follow the M\=\=> V \=\=> C\=\=> cycle .

  ```python
  lists/views.py
  from django.http import HttpResponse
  from django.shortcuts import render

  def home_page(request):
      if request.method == 'POST':
          return HttpResponse(request.POST['item_text'])
      return render(request, 'home.html')
  ```

  ​


* We first do a functional test, to see if we could have a right result. Then we do unit test to see if every part works as expected. ---- This is for this simple code only. For complicated one, we still will do test first then coding work.

## 4 More code in tests

  In the unit test, add another function to test the new logic part of the new code in html:

```python
def test_home_page_can_save_a_POST_request(self):
    request = HttpRequest()
    request.method = 'POST'
    request.POST['item_text'] = 'A new list item'

    response = home_page(request)

    self.assertIn('A new list item', response.content.decode())
```

The organize of the above code, divided by two empty lines, is setup the test, call the test and assertion results. That's the general pattern of the code.







## 5 The second round of test/code

The next stop is to test the logic of post. we start from unit test by adding:

```python
lists/tests.py
from django.template.loader import render_to_string #Not sure if the book missed this line
    self.assertIn('A new list item', response.content.decode())
    expected_html = render_to_string(
        'home.html',
        {'new_item_text':  'A new list item'}
    )
    self.assertEqual(response.content.decode(), expected_html)
```



* The working principle: We are going to test whether the return of the POST is the same as what we want: return of the POST is response.content.decode(), which is directly get from the output. And the expected value is expected_html, which is generated from render_to_string(), and the string to render is, according to the code, from "home.html". 
* A question maybe asked here, is there is no code in "home.html" give "A new list item". So why the output from Django returns such a string? (Otherwise, assertion wouldn't be true) This has something to do with Template function of Django. This function allow Django to render/replace some of the html content in the runtime, allowing a more flexible output and is activated by using "{{...}}" symbol. More on it is here: https://docs.djangoproject.com/en/1.9/topics/templates/



The assertion error that showed up. And to eliminate it, we need to change view.py:

```python
def home_page(request):
    return render(request, 'home.html', {
        'new_item_text': request.POST.get('item_text', ''),
    })
```



* After passing those unit tests successfully, we then go for the functional test. And we still get error like " couldn't find new item ". This is because we haven't enumerate the table yet (no code in html). Instead of coding html, we use following code to get it pass and will code more in the html later.

``` html
<html>
    <head>
        <title>To-Do lists</title>
    </head>
    <body>
        <h1>Your To-Do list</h1>
        <form method="POST">
             <input name="item_text" id="id_new_item" placeholder="Enter a to-do item" />
             {% csrf_token %}
        </form>

        <table id="id_list_table">
        <tr><td>1: {{ new_item_text }}</td></tr>
        </table>

    </body>
</html>
```



* You could imagine that in the functional test, we are going to test each of every item in the table, which will be exactly the same code just with different "new_item_text". So we are going to do a bit code refactoring in the functional test. What we are going to do is to define the similar part as a new function ( check_for_row_in_list_table() ) and just vary with the different "new_item_text" content.

  the whole functional test should now looks like this:

  ``` python
  from selenium import webdriver
  from selenium.webdriver.common.keys import Keys
  import unittest

  class NewVisitorTest(unittest.TestCase):

      def setUp(self):
          self.browser = webdriver.Chrome("C:\\Users\\Odysseus\\Downloads\\chromedriver.exe")
          self.browser.implicitly_wait(3)

      def tearDown(self):
          self.browser.quit()

      def check_for_row_in_list_table(self, row_text):
          table = self.browser.find_element_by_id('id_list_table')
          rows = table.find_elements_by_tag_name('tr')
          self.assertIn(row_text, [row.text for row in rows])

      def test_can_start_a_list_and_retrieve_it_later(self):
          # Edith has heard about a cool new online to-do app. She goes
          # to check out its homepage
          self.browser.get('http://localhost:8000')

          # She notices the page title and header mention to-do lists
          self.assertIn('To-Do', self.browser.title)
          header_text = self.browser.find_element_by_tag_name('h1').text
          self.assertIn('To-Do', header_text)

          # She is invited to enter a to-do item straight away
          inputbox = self.browser.find_element_by_id('id_new_item')
          self.assertEqual(
                  inputbox.get_attribute('placeholder'),
                  'Enter a to-do item'
          )

          # She types "Buy peacock feathers" into a text box (Edith's hobby
          # is tying fly-fishing lures)
          inputbox.send_keys('Buy peacock feathers')

          # When she hits enter, the page updates, and now the page lists
          # "1: Buy peacock feathers" as an item in a to-do list table
          inputbox.send_keys(Keys.ENTER)
          self.check_for_row_in_list_table('1: Buy peacock feathers')

          # There is still a text box inviting her to add another item. She
          # enters "Use peacock feathers to make a fly" (Edith is very
          # methodical)
          inputbox = self.browser.find_element_by_id('id_new_item')
          inputbox.send_keys('Use peacock feathers to make a fly')
          inputbox.send_keys(Keys.ENTER)

          # The page updates again, and now shows both items on her list
          self.check_for_row_in_list_table('1: Buy peacock feathers')
          self.check_for_row_in_list_table('2: Use peacock feathers to make a fly')
          
          # Edith wonders whether the site will remember her list. Then she sees
          # that the site has generated a unique URL for her -- there is some
          # explanatory text to that effect.
          self.fail('Finish the test!')

          # She visits that URL - her to-do list is still there.
  ```

  Then we run the functional test and you will get some error like this:

  ```Python
  Failure
  Traceback (most recent call last):
    File "C:\Users\Odysseus\PycharmProjects\superlist\functional_test6.py", line 53, in test_can_start_a_list_and_retrieve_it_later
      self.check_for_row_in_list_table('1: Buy peacock feathers')
    File "C:\Users\Odysseus\PycharmProjects\superlist\functional_test6.py", line 17, in check_for_row_in_list_table
      self.assertIn(row_text, [row.text for row in rows])
  AssertionError: '1: Buy peacock feathers' not found in ['1: Use peacock feathers to make a fly']
  ```

  * All right, now we start to follow TDD (or rather, error-driven development EDD) to the next move.

  ​

  This to tell us, it is more convenient to let database handle those tables. And the one that handles how table is stored in the database is the layer called ORM (Object-Relational Mapper), since it is essentially a mapping between data to the table(or whatever the data structure is).

## Commit before diving into constructing a database

  To sync between the  local copy and the one on  "github.com", we do:

  ``` powershell
  # record all of the changes
  git add .

  # save the record by using "commit", and "-m" will give the comment
  git commit -m "all the way until incorporating the database"

  # push the local change (master branch) to the origin branch at github.com 
  git push origin master

  ```

  ​


