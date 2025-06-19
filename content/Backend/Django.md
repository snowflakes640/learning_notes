### Beginning

Better to start a python venv. It's easy. Just open the command palette and search for creating an environment. Then find the interpreter from the available options and boom.

#### <u> Creating the project for main web app </u>

`django-admin:` To see the sub commands inside it
`startproject`: The full command would be `django-admin startproject <project name>` This will do the magic of creating the base structure of the project.

Inside the project we would have:
- a folder with the same name as the project name. This will have following files initially-
	- \_\_init\_\_.py : Apparently it's just an empty file :o However important to let python know that is a module
	- settings.py : We're going to set all our configurations (installed apps or DBMS) and stuff here.
	- urls.py : Another file we will be playing a lot with. We organize all the urls here.
	- wsgi.py : Some pieces of code to set the communication between the server and our web application.
-  manage.py: They have written some magic spell I am yet to properly know. Let's just assume they wrote it for our own (project's) good and move on (for the time being). We'll be running `python manage.py runserver` a lot of times though, to keep run our server.

---

#### <u> Creating a web app inside the main app </u>

Inside this website project, we can have many apps. For the time being, with the tutorial I am following (Corey's YT channel), we will be creating a blog app. We can use this app in multiple websites too. For this we will be using `python manage.py startapp <name>`  (for me the name is blog_app)
So this will give us a new set of files under the app command - 
- \_\_init\_\_.py
- admin.py
-  apps.py
- models.py
- tests.py
- views.py
- migrations

---

#### RUN 1 <u> Setting the urls to navigate to the response. </u>

So there would be many coming backs here, I will be trying to write each run with the reason and stuff.
First, we will create a function (names "home") in the *views.py* file to handle the response I am trying to show in my home page. Then we would need to add it to the url mapping to actually make it available. So, we would be creating a new *urls.py* inside the app and add the "home" function inside the url path. As this home page is like the root of all pages we can map just " " for this function and that would made it the root page of the blog app.
```python
path('', views.home, name="blog-home"  ),
```

Now this is the root of the app but we still need to integrate it to our main web app. To do that we would add this line into the main web app urls.
```python
path("blog/", include("blog_app.urls"))
```
What this does is, let the web know that if we have "blog/" in the url, it has to connect the path to the *blog_app.urls* file. And then the rest of the url would have to be handled the way it is written inside that *blog_app.urls* file.  Now with the "blog" in url we would see our predefined "home" function response being loaded.

> [!summary] walkthrough of loading the home page
> First of all, we define a function ("home") to show in the homepage of blog app (inside *blog.urls*) -> When we write `address/blog/` it firsts check the main *urls.py* file to see if there is any section matching the portion after the main address (the "blog/" part) -> it gets the match and navigates it to the *app.urls* file -> there it matches the portion after "blog/" (nothing, in this case) and sees that it needs to load the *views.home* function -> It finds the home function from that file and loads what response is written there (a string, in this case) and loads the httpResponse

---

#### RUN 2 <u> Adding templates </u>

Django looks for a `templates` sub-directory in each of the apps. So, we would creates a sub-directory inside the blog-app named `templates`. Now, as there might be multiple apps inside the main app and each of them should have a templates sub-dir, it is convention add one more sub-directory inside the `templates` sub-dir with the same name as the app. This would make the identification of the template for a particular app easier. Then we would create html files for the response we want to load for the pages (`home` and `about` for now).

To let django know that this is an app and treat like one (make it look for the `templates`, make it look for the database) it is recommended to add this app into the main app configuration. So, we go to `settings.py` and add this into the installed apps list:
```python
INSTALLED_APPS = [

	"blog_app.apps.BlogAppConfig",  #The "BlogAppConfig" is found from the `apps.py` file inside the app
    ... ]
```

Till now we were only returning a string as HTTPResponse from our *home* function. But when we have to respond with more complicated html pages ( as we set up in the `templates` pages), we will using *render* like this:
```python
def home (request):
	 context = {
        "posts" : posts
    }
    return render(request, "blog_app/home.html", context) 
    # Inside the render, 1st arg is always "request", 2nd arg is the html page we want to load and, 3rd arg is the dictionary we want to pass to convey info
```

We can use the context as the access point to the data that are being passed around.

To avoid writing same code multiple times we are creating a file `base.html` it will basically hold the content that we are gonna reuse multiple times along with allowing us to over-write content blocks with unique codes from each html files. There is a "block content" that signifies the block that is to be re-written to include the unique codes of different html files. Including both of the snippets of `base.html` and modified `home.html` 

![[base_html.png]]
![[home_html.png]]
{We will also be adding some bootstrap styling but details of that's for another note}


> [!tip] url setup
> Using a name tag while creating urls can be later easily used to reference(`href`) these urls for different pages. For example:
> Writing this: `path('', views.home, name="blog-home"  ),`
> Can now be used in this: `<a class="nav-item nav-link" href="{% url 'blog-home' %}">Home</a>`

---

#### RUN 3 <u> Quick look at the admin page </u>

Django uses this *makemigrations* and *migrate* command from manage.py to detect and apply changes of the database. It's like github's staging and then pushing the commits.
In order to use the admin page, we first of all would need an admin (duh), but without creating a database we cannot store the info about the admin. So first we use migration, which will create the database on the first run and then proceed to make admin. And just `python manage.py createsuperuser` command is enough to create the admin and access the page. Cool thing is, django on its own handles the password security, checks it, verifies the ones with "staff access", specifying authority for specified users and all that. ~~i'll keep my pw here "testing321" cz my awesome memory feels insulted to keep it safe.~~

---

#### RUN 4 <u> Database and Migrations </u>


> [!NOTE] ORM
> Object Relational Mapper. Basically it just helps us to access the database in an object oriented way. Also lets us use different databases for development or production. For this to apply we would create this databases in a class-way, naming in "models".

Django has already created a table for the users that we have already put into use in the previous run. For the time being we would be creating a table (in the database) for the posts. So we will just be creating a class (table in db) that inherits from the Django models. The attributes inside the class would be the fields of the table. We can see the SQL code to create this migrations. The command would be `python manage.py sqlmigrate <app_name> <migration number>`
See, how we did some SQL thing without even knowing abc of SQL *huhuhahahaha*
Another good thing about *migration* is it allows us to modify the database even after it has been created. Normally we would have to write complicated code to update it without messing the current codes. However, with *migration* it will do it for us *UwU*

We can see the models we have created from the admin page. For that though, we have to go to *admin.py* and write this little piece of code `admin.site.register(Post)`.  After importing the models here.

We can use `python manage.py shell` to talk to the databases through python shell. TBH I hate this portion so I will try to write this section only when I would absolutely have to :>

Alright so first we import both( for this case) the data table we have. Like this, `from blog_app.models import Post`. Then we write out queries and se results. Some of the queries look like:
- User.objects.all()
- User.objects.first()
- User.objects.filter(username="snow")
The object/s returned by this query, we can grab them into a variable then see different features of it.
Again from the shell we can new objects like here we can create a post, save it and then view it. like:
```shell
	user1 = User.objects.filter(username="snow").first()
	post1 = Post(title="Blog 1", content="First Post Content", author=user1)
	post1.save()      
	Post.objects.all()
output : <QuerySet [<Post: Post object (1)>]>
```
And then there are lots of things to do from user to post to user. WE can ig use google until we get better at it.

cool. Now when we load something, we want to use the info from database. To pull up the infos we can use this query. For example, in *RUN 2* we have used `context` to pass some data to the page. Now, we would pass our query result as the context, like, `context = { "posts" = Post.objects.all()}` Anddd, bingo now we are loading the data from our database.

---

#### RUN 5 <u> User Registration </u>

We can use a new app to keep track of the users. 
Among many different aspects we will be using forms to create new users. Good thing is Django already provides a form to do this.

In a template we will be creating a form template that would be rendered if the register page is visited. Here we are just adding some lines and the "crispy" formatting which will make our form look pretty. We would also be adding the CSRF Token
 
> [!NOTE] CSRF Token
> Cross Site Request Forgery token. We need to add this token to prevent some certain attacks from our forms.

on a side note, I wasted 20 minutes on debugging cause I was putting a whitespace inside my username which was not allowed. However, at least now I know that I can see the error uing `print(form.errors)`

Then in `view` file, we would be creating a register view. Thankfully GPT wrote me a better explanation and I would be just pasting it here :) sorry but I am lazy.
This code defines a `register` view in Django for handling user registration. Here’s how it works:

1. **Check the Request Method**:
    - The view first checks whether the incoming HTTP request is a `POST` request (`if request.method == "POST"`).
    - When you click the **submit button** on the form, it sends the form data to the server using the `POST` method. This is why we check for `POST` before processing the form data.
2. **Handling the `POST` Request**:
    - When the method is `POST`, the view assumes the user submitted the form. It creates an instance of the `UserCreationForm` class, passing in the submitted form data (`request.POST`).
    - `form.is_valid()` is called to validate the submitted data. If the data passes all validation rules:
        - The `form.cleaned_data` dictionary is populated with the validated form fields.
        - The `username` is extracted from `form.cleaned_data` using `.get("username")`.
        - A success message is flashed using the `messages.success()` function, informing the user that their account has been created.
        - The user is redirected to the `blog-home` page using `redirect("blog-home")`.
3. **Handling the `GET` Request**:
    - If the request method is **not `POST`** (usually a `GET` request):
        - The view creates an empty instance of the `UserCreationForm` class and renders it in the `users/register.html` template.
        - This is how the blank form is presented to the user when they first visit the registration page.
4. **Returning the Template**:
    - Regardless of whether the request is a `POST` or `GET`, the form is passed to the template (`"users/register.html"`). The template then renders the form for the user to interact with.

---

#### RUN 6 <u> Login & Logout System </u>

This is really just setting the views with login system and adding some `html` pages to render.



