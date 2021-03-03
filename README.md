### This repo is about a Django project with a pizza ordering website.
- [ ] Create virtual environment as a best practice:
```py
python3 -m venv env
```
- [ ] Activate scripts:
```bash
.\env\Scripts\activate
```
- Install django:
```bash
pip install django
```
- See installed packages:
```sh
pip freeze
```
- Create requirement.txt same level with working directory, send your installed packages to this file, requirements file must be up to date:
```py
pip freeze > .\requirement.txt
```
- Create project:
```py
django-admin startproject nadiasgarden
```
- Various files has been created!
- change the name of the project main directory to distinguish from subfolder with the same name!
```bash
mv .\nadiasgarden\ src
```
- Lets create first application:
- Go to the same level with manage.py file:
```bash
cd .\src\
```
- Test django project is working or not:
```py
python manage.py runserver
```
- Check http://localhost:8000/ and if you can see the rocket its nice!
### Nadia's Garden has lots of features but the main thing is ordering pizza. Let's create an app for that:
- Start app for ordering named "pizza":
```py
python manage.py startapp pizza
```
- We need to have a home page and an ordering page. So we need to start with urls.py to apply for those.
- Add this lines:
```py
from pizza import views  # The templates will be rendered from pizza app views.py

urlpatterns = [
    path('', views.home, name='home'),  # This is for home page
    path('order/', views.home, name='order'),  # This is for ordering page
]
```
- Go to settings.py and add 'pizza', under INSTALLED_APPS, don't forget to put comma after.
- Go to views.py in pizza app and create first views by adding:
```py
def home(request):
    return render(request, 'pizza/home.html')

def order(request):
    return render(request, 'pizza/order.html')
```
- We have already show the views result, now it's time to create home.html and order.html templates.
- Create templates/pizza recursive directory under pizza. Then, create home.html:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nadia's Garden</title>
</head>
<body>
    <h1>Nadia's Garden</h1>
    <a href="{% url 'order' %}">Order a pizza</a>
</body>
</html>
```
- Create order.html:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Order</title>
</head>
<body>
    <h1>Order a pizza</h1>
</body>
</html>
```
- Save all and see what is the result from http://localhost:8000/  If you didn't run server again:
```py
python manage.py runserver
```
- Modify order.html, add form:
```html
<form>
    <label for="topping1">Topping 1: </label>
    <input id="topping1" type="text" name="topping1">
    <label for="topping2">Topping 2: </label>
    <input id="topping2" type="text" name="topping2">
    <label for="size">Size: </label>
    <select name="sizse" id="size">
        <option value="Small">Small</option>
        <option value="Medium">Medium</option>
        <option value="Large">Large</option>
    </select>
</form>
```
- Added a simple form for selecting toppings and size. Lets add an order button.
