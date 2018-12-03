+++

title = "Notes to Test driven development with Django using pycharm (chp 2-3)"

description = ""

tags = ["Python","Pycharm", "Django"]

categories = [ "My_note","Development" ]

date = "2016-07-08"

toc = true 

+++



## 0 Checkpoints

* Blackbox Test=Functional Test=Acceptance Test= End-to-End Test ; These names are referring to the same thing. The purpose of this kind of test is to look at how the whole application functioned from outside/custom's point of view. The term black box test is forged is because we just want to test the outcome of the whole code without knowing the details inside
* App = function;  Structure the code into several apps/ functions (def function()). In this way, the whole program is build up like Lego's toy. You could easily use other's Lego block or even the one you used before.The advantages are many, but one of them could tell you that they just save monk.


* Coming up with a "user story", to help with function design and developmental flow as well as the interface design.


* Unit test library from Django library
* Get started with a minimum possible app
* Further development by unit-test \=\=> coding \=\=> unit test \=\=> coding ... cycle

## 1 Get started with an app of minimum possible function

The minimum function is actually "after started, serve an empty webpage." We already did this in chp1, but the word "it works" and the title "Django" are from the Django. However, here we want to give out the message ourselves. 



* Remember, this is called test driven development, so we should first write the test and then write the code of the function you want to add to the program. Then keep revising it until the code passes the test. However, the problem is, sometime you focused on the test but lost the big picture of how software is going to evolve. Well, that's where the user/customer's story get in. The user's story will help you to find out what's the next step you should take. 
* Here, I re-organize the logic flow of the book. I first go to chp3 to create a new app by copying the "template" and then back to chp2 to construct functional test then back to chp3 again to construct the unit test. Although this would make no "test-driven" feeling, it put the two tests side by side and convenient for first time user to compare them.



* To start a new app, we need to change the parameter in command line to "startapp lists". The "startapp" is the parameter that ask manage.py to copy out template to a new app with the name "lists". You could edit the configuration of running mange.py from run menu or use command line:

  ```python manage.py startapp lists```

* Now you should have a new folder created in the same level of superlist (located at \$PycharmProjects\superlist\*.\*) and they both under another superlist folder (located at \$PycharmProjects\)



![tdd-django-pycharm-2-1.png](/images/tdd-django-pycharm-2-1.png)



## 2 Add a little more function to list app

* The Django is a web framework that uses MVC pattern, which is stand for "Model-View-Controller".  Here provides a simple explanation:

  http://www.dummies.com/how-to/content/the-modelviewcontroller-mvc-design-pattern.html 

* Workflow of Django framework :

  * An HTTP request from a user (a browser or other)
  * Django uses some rules to direct the request to proper view/webpages 
  * The view function return an HTTP response accordingly

* So according to the workflow, we need to test two things:

  * directing function : whether it would lead user to proper view
  * whether the view return the correct response

## 3 A "got-to-fail" unit test for the first app

* I start with unit test, because I don't quite agree with the book "Once we have a functional test that fails, we start to think about how to write code that can get it to pass (or at least to get past its current failure)". To me, even if functional test is good to pass, you would still need to do more, since there is no guarantee that the internal of the system works as expected.

* Create the tests.py under lists with followings:

```
  from django.test import TestCase
  class SmokeTest(TestCase):
  		def test_bad_maths(self):
  			self.assertEqual(1 + 1， 3)
```

  Here we use the unit test library provided by Django itself. To let it run, we have to modify the parameters for manage.py to be "test" or in CLI:

```
  python manage.py test
```
  The results:

  ![tdd-django-pycharm-2-2.png](/images/tdd-django-pycharm-2-2.png)

* Although result is "failure=1", it is what we expected, since "1+1 =3" is not true. As author said, it is one of so-called expected error. It only means we have control here. However, the purpose of all of tests is the whole program is working as expected. We need to, not only test the case that the code gives successful outputs, but also would like to see, if code would fail when we design it to fail. 

## 4 Unit test for the first point: direct request to a correct view  

* We now modify the tests.py to test the first point:

```
    from django.core.urlresolvers import resolve
    from django.test import TestCase
    from lists.views import home_page	
 
    def test_root_url_resolves_to_home_page_view(self):
        found = resolve('/')
        self.assertEqual(found.func, home_page)
```



Here uses some internal function from Django. resolve() is to resolve the request to its corresponding view page. So found should store the result of resolving. Then we use assert to judge, whether the result of resolving "/" is home_page.



* Test driven :

  * If you run the code, you would know you will have an error. The reason is we haven't setup the home_page yet. So there of course would be an error. But we follow this error to our next stage, which is make a home_page. This working principle is called test driven. You encounter an error, no matter it is something you haven't done or already done, test points out your next step. You forward by fixing those errors.

* Next step:

  * We create the home_page:

    in list/views.py:

```
    from django.shortcuts import render
    def home_page():
    	pass
```

  * So,we fix the "view", now go to "control":

    in superlist/urls.py:

```
    from django.conf.urls import include, url
    from django.contrib import admin

    urlpatterns = [
      	#url(r'^admin/', include(admin.site.urls)),
      	url(r'^$', views.home_page, name = 'home'),
    ]
```



​           You should pass the unit test now. The stuff we are doing above, is to tell Django where to direct the requests. if there is coming request asking for '/', then directs it to view.home(), which we just defined in view.py. ("def home" line) There are two ways to add resolving: function views and class-based view. This is written in the comments in "urls.py".



## 5 design a test then design the corresponding code to give the expected result

So we start a new unit test and then use test-driven method to continue.

* the new unit test, writing the list/tests.py:

```python
  from django.core.urlresolvers import resolve
  from django.test import TestCase
  from django.http import HttpRequest

  from lists.views import home_page

  class HomePageTest(TestCase):

      def test_root_url_resolves_to_home_page_view(self):
          found = resolve('/')
          self.assertEqual(found.func, home_page)

    def test_home_page_returns_correct_html(self):
          request = HttpRequest()  
          response = home_page(request)  
          self.assertTrue(response.content.startswith(b'<html>'))  
          self.assertIn(b'<title>To-Do lists</title>', response.content)  
          self.assertTrue(response.content.endswith(b'</html>'))  
```


* The above code , the extra code compared to the last version to be precise,  simulates a browser to send a HTTP request to "/" and test whether the response it returns would be the same as several assertions assumed.

* You would now have trouble run the unit test. (Remember the way to run unit test is to give "test" as command line parameter to manage.py)


* Now we demonstrate the test-driven method. Or here is unit-test \=\=> coding \=\=> another unit-test \=\=> coding ... cycle


* The first error you got is you TypeError, telling you that home_page() takes 0 positional arguments but 1 was given.

  * This is easy to fix, we change the home_page() function to accept argument:

```python
    lists/view.py:
    def home_page(request):
        pass
```



* The second error you got should be an "assertion error" which make sense, since we didn't give any contents to home_page (only a "pass" ). So it couldn't match any assertion command.

  * This is fixed by:

```python
    list/views.py:
    from django.http import HttpResponse
    def home_page(request):
        return HttpResponse()
```



* The third error, is "False is not true". When you see this, you should know we are close. This means, the Django now returns something. However, It is not return something it suppose to.

  * fix:

```python
    lists/views.py
    def home_page(request):
    	return HttpResponse('<html>')
```

    The reason we give it "<html>" is to cater what we write in the unit test. Our sole purpose is to pass the unit test. And this "desire" would driven us finish the whole development.
    
    ​


* The next two "hotfixs" are basically to solve the rest of the assertion in the unit test.

```python
  lists/view.py
  def home_page(request):
      return HttpResponse('<html><title>To-Do lists</title></html>')
```

  We don't have to write three separate HttpResponse(), because one would contains all of the elements that assertions are looking for. However, it doesn't hurt that you could try to write three separate HttpResponse() with each correponds to one assertion.

## 6 Push the codes to github.com

* After finishing a basic code, we push all code to github.com which helps in version control. 

* Additionally, when asked to provide a name for the new git warehouse, on the same webpage, you could find a small checkbox on the bottom to let you choose to create READMT.md. Don't choose this! Just create an empty git warehouse. You can add "Readme.md" later. Or you could have a non-forward error. And solution is the do a "fetch" or "Pull+Merge" but why not do it easier.

```
    # this is to initialize the git warehouse
    git init 

    # this is to add everything to current version 
    git add .

    # this is to add comment to current version so later it helps us to remember
    git commit -m "first commit: contains a basic unit test and basic view.py"

    # this is to link the local git warehouse with the remote one on github.com (need register! add create a new git warehouse)
    git remote add origin https://github.com/YOUR_USER_NAME/superlist.git

    # this is to push local change (master branch) to github (origin branch)
    git push origin master
```



