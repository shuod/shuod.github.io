+++

title = "Notes to Test driven development with Django using pycharm (chp 5 database!)"

description = ""

tags = ["Python","Pycharm", "Django"]

categories = ["My_note",   "Development"]

date = "2016-07-19"

toc = true 

+++



## 0 Checkpoints

* ORM model in Django

## 1 The first unit test 

First, let's write the unit test for database. What are we going to test then? Well, as we discussed in the last section, the unit test is about testing the logic, flow control. So what we are going to do here is to test:

* We input a value as the first row in the webpage. Does the database receives the same value as the input?Assuming the input value from web already in a variable, we try to put the value this variable into the database. (We will test whether the value from web is stored into that variable later.)
* We input, say 2 records, does the database properly saved 2 records? Again, we just assume the values from web interface already properly saved into two variables. We just verify whether the values in two variables are the same as they are in the database.

Having the two above ideas in mind, we design the unit test as the following:

```python
class ItemModelTest(TestCase):

    def test_saving_and_retrieving_items(self):
        # Creating the test
        first_item = Item()
        first_item.text = 'The first (ever) list item'
        first_item.save()

        second_item = Item()
        second_item.text = 'Item the second'
        second_item.save()

        # Call the function under the unit test, provided by Django
        saved_items = Item.objects.all()
        self.assertEqual(saved_items.count(),2)

        # Assertions
        first_saved_item = saved_items[0]
        second_saved_item = saved_items[1]
        self.assertEqual(first_saved_item.text, 'The first (ever) list item')
        self.assertEqual(second_saved_item.text, 'Item the second')
```



We still follow the steps for writing the code for a unit test:

* First part: create the test
* Second part: call the function under the unit test(usually provide by Python standard lib or the framework)
* Third part:Assertions

## 2 Test-driven (or Error-driven) by the first unit test

Remember, one of the ways to run a unit test in Pycharm is "Tools->Run manage.py task" then on the bottom window input test as the parameter:

![tdd-django-pycharm-4-0.png](/images/tdd-django-pycharm-4-0.png)

* If you could follow the unit test code, you will find we haven't written some of the class/functions yet. So we expect some error will be popped up, no surprise:

![tdd-django-pycharm-4-1.png](/images/tdd-django-pycharm-4-1.png)

The way to fix it is simply add the functions the unit test needs(coding modes.py) as well as their method:

```python
(lists/models.py)
from django.db import models
class Item(models.Model):
    pass
```



* Then, you will faced with another error, which tells you that the database hasn't been created yet:

![tdd-django-pycharm-4-2.png](/images/tdd-django-pycharm-4-2.png)

So we just create one for it by using manage.py with "makemigrations" parameter:

![tdd-django-pycharm-4-3.png](/images/tdd-django-pycharm-4-3.png)

Then you should have a new folder under the lists with the name "migrations":

![tdd-django-pycharm-4-4.png](/images/tdd-django-pycharm-4-4.png)



* Then, you will go to next error:
  ![tdd-django-pycharm-4-5.png](/images/tdd-django-pycharm-4-5.png)


This is due to the fact, you probably have a guess already, we haven't setup the column other than the default "ID" column. So we need to create a column called "text" to hold the values passed from the web interface:

```python
(lists/models.py)
class Item(models.Model):
    text = models.TextField(default='')
```

Other type of field, since a database should have the ability to store many types of data, will include: IntegerField, CharField, DateField, TestField, etc.

But this is not done yet. After changing the models.py, we should update the table with this new model and this can be done by another "makemigration" command. And now the unit test should have no problem to pass.

![tdd-django-pycharm-4-6.png](/images/tdd-django-pycharm-4-6.png)



## 2 The second unit test

Now, we go one step further. We will test whether the input from the web page has been properly stored into the variable. (The exact variable we test in the first unit test.)

```python
 def test_home_page_can_save_a_POST_request(self):
        request = HttpRequest()
        request.method = 'POST'
        request.POST['item_text'] = 'A new list item'

        response = home_page(request)

        self.assertEqual(Item.objects.count(), 1)
        new_item = Item.Objects.first()
        self.assertEqual(new_item.text, 'A new list item')

        self.assertIn('A new list item', response.content.decode())
        expected_html = render_to_string(
            'home.html',
            {'new_item_text': 'A new list item'}
        )
        self.assertEqual(response.content.decode(),expected_html)
```

* the error would be "assertion error 0!=1 ". So we follow this to modify view.html to let it match to what the assertion expected to be.





















