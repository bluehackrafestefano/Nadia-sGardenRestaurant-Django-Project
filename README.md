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
```html
<input type="submit" value="Order Pizza">
```
- When you click "Order Pizza" button some interesting text show up here in the top.
- Add action to form to specify where to send these form.
```html
<form action="{% url 'order' %}" method="GET">
```
- When its a get request the change can be seen on url.
- POST method is more appropiate for us.
```html
<form action="{% url 'order' %}" method="POST">
```
- Enter some selections and click order pizza, and it gives error: CSRF verification failed. Request aborted.
- Get rid of this by adding the firs line of our form:
```html
{% csrf_token %}
```
### Adding forms:
- There is better and siple way to create order page. Add forms to the project. Create forms.py under pizza dir.
```py
from django import forms

class PizzaForm(forms.Form):
    topping1 = forms.CharField(label='Topping 1', max_length=100)
    topping2 = forms.CharField(label='Topping 2', max_length=100)
    size = forms.ChoiceField(label='Size', choices=[('Small', 'Small'), ('Medium', 'Medium'), ('Large', 'Large')])
```
- Now we have new class PizzaForm, return to views.py and modify the order function.
```py
from .forms import PizzaForm
def order(request):
    form = PizzaForm()
    return render(request, 'pizza/order.html', {'pizzaform':form})
```
- No we don't need manually created labels and selections inside order.html, comment out this part. Just add {{ pizzaform }} to place our PizzaForm class form here:
```html
{{ pizzaform }}
<!-- <label for="topping1">Topping 1: </label>
<input id="topping1" type="text" name="topping1">
<label for="topping2">Topping 2: </label>
<input id="topping2" type="text" name="topping2">
<label for="size">Size: </label>
<select name="sizse" id="size">
    <option value="Small">Small</option>
    <option value="Medium">Medium</option>
    <option value="Large">Large</option>
</select> -->
```
- Result is more clean code!
### Capture the order
- In the views.py we can modify order function to distinguish bw get and post requests.
```py
from django.shortcuts import render
from .forms import PizzaForm  # referring to newly created forms.py and our new PizzaForm

def home(request):
    return render(request, 'pizza/home.html')

def order(request):
    if request.method == 'POST':
        filled_form = PizzaForm(request.POST)
        if filled_form.is_valid():
            note = 'Thanks for ordering! Your %s, %s and %s pizza is on its way!' %(filled_form.cleaned_data['size'], 
                                                                                    filled_form.cleaned_data['topping1'], 
                                                                                    filled_form.cleaned_data['topping2'],)
            new_form = PizzaForm()
            return render(request, 'pizza/order.html', {'pizzaform':new_form, 'note':note})
    else:
        form = PizzaForm()
        return render(request, 'pizza/order.html', {'pizzaform':form})
```
- Need to modify order.html accordingly, to see the thanks note in the page, add the script below:
```html
<h2>{{ note }}</h2>
```
## Need to create a model to keep order info
- Open models.py
- Create classes for size of the pizza, toppings, and make some relation to Size and others:
```html
class Size(models.Model):
    title = models.CharField(max_length=100)
    
    def __str__(self):
        return self.title  # This is for good visual experimentation!

class Pizza(models.Model):
    topping1 = models.CharField(max_length=100)
    topping2 = models.CharField(max_length=100)
    size = models.ForeignKey(Size, on_delete=models.CASCADE)  # This is for correlation to the Size class
```
- We want these two newly created class to be shown on admin panel. So, modify the admin.py:
```py
from .models import Pizza, Size

admin.site.register(Pizza)
admin.site.register(Size)
```
- After these modifications, need to migrate them!
```py
python manage.py makemigrations
python manage.py migrate
```
- Need a superuser to enter admin page:
```py
python manage.py createsuperuser
```
- Run the server and go to admin page.
- Create sizes, user cant do that, admin must create them.
### Modify forms according to models
- Since we have new models, lets update forms accordingly, things will be easier.
```py
from django import forms
from .models import Pizza

# class PizzaForm(forms.Form):
#     topping1 = forms.CharField(label='Topping 1', max_length=100)
#     topping2 = forms.CharField(label='Topping 2', max_length=100)
#     size = forms.ChoiceField(label='Size', choices=[('Small', 'Small'), ('Medium', 'Medium'), ('Large', 'Large')])

class PizzaForm(forms.ModelForm):
    class Meta:
        model = Pizza
        fields = ['topping1', 'topping2', 'size']
```
- Look at the order page and you'll see the exact same page! With very simple code.
- We can add labels to our form to see better text on the page:
```py
labels = {'topping1':'Topping 1', 'topping2':'Topping 2'}
```
- You can do any specific change, dive deep into forms! Use widgets!
- Bring back the first version of the forms:
```py
topping1 = forms.CharField(label='Topping 1', max_length=100, widget=forms.Textarea)
```
- With Textarea we can see a bigger box.
```py
topping1 = forms.CharField(label='Topping 1', max_length=100, widget=forms.PasswordInput)
```
- With PasswordInput the text user typed will be hidden.
- Also we can get rid of toppings and make a multiple choice toppings list can be selected:
```py
# This is the third version:
class PizzaForm(forms.Form):
    # topping1 = forms.CharField(label='Topping 1', max_length=100, widget=forms.PasswordInput)
    # topping2 = forms.CharField(label='Topping 2', max_length=100)
    toppings = forms.MultipleChoiceField(choices=[('pep', 'Pepperoni'), ('cheese', 'Cheese'), ('olives', 'Olives')])
    size = forms.ChoiceField(label='Size', choices=[('Small', 'Small'), ('Medium', 'Medium'), ('Large', 'Large')])
```
- This is cool but user cant select multiple bcoz they cant know Ctrl + click to select. So need to add something extra:
```py
toppings = forms.MultipleChoiceField(choices=[('pep', 'Pepperoni'), ('cheese', 'Cheese'), ('olives', 'Olives')], widget=forms.CheckboxSelectMultiple)
```
- Widget can be added not only regular form but also can be used with model form:
```py
# This is the fourth version:
class PizzaForm(forms.ModelForm):
    class Meta:
        model = Pizza
        fields = ['topping1', 'topping2', 'size']
        labels = {'topping1':'Topping 1', 'topping2':'Topping 2'}
        widgets = {'topping1':forms.Textarea, 'size':forms.CheckboxSelectMultiple}
```
- With Textarea widget, we can see a bigger box. And select multiple choice size.
```py
from django import forms
from .models import Pizza, Size

# This is the fourth version:
class PizzaForm(forms.ModelForm):
    
    # This is our change, dont wanna empty label, good to see multiple choice check box widget
    size = forms.ModelChoiceField(queryset=Size.objects, empty_label=None, widget=forms.CheckboxSelectMultiple)
    
    class Meta:
        model = Pizza
        fields = ['topping1', 'topping2', 'size']
        labels = {'topping1':'Topping 1', 'topping2':'Topping 2'}
```
- Need modification because dont want customer to select more than one size on one order!
```py
size = forms.ModelChoiceField(queryset=Size.objects, empty_label=None, widget=forms.RadioSelect)
```
### Accept files from user
- This can be possible via forms. Go to order.html, modify form element and add enctype.
- When the value of the method attribute is post, enctype is the MIME type of content that is used to submit the form to the server. Possible values are:
    * application/x-www-form-urlencoded: The default value if the attribute is not specified.
    * multipart/form-data: The value used for an <input> element with the type attribute set to "file".
    * text/plain: (HTML5)
This value can be overridden by a formenctype
```html
<form enctype="multipart/form-data" action="{% url 'order' %}" method="POST">
```
- Need to install pillow, cd .. and then activate environment and:
```py
pip install pillow
```
- Modify forms:
```py
image = forms.ImageField()
```
- We are not ready to accept files. Need to modify views.py:
```py
filled_form = PizzaForm(request.POST, request.FILES)
```
- We didnt save the files but ready to do that.
- First go back to normal, erase request.FILES from views, and image = forms.ImageField() from forms.py, and enctype="multipart/form-data" from order.html. We turned back to normal.
### From Sets
- Repeat one form again and again with form sets. Customer wants to order multiple pizzas at once.
- Modify order.html, add after the first form element the secong multiple form:
```html
<br><br>

Want more than one pizza?

<form action="{% url 'pizzas' %}", method="GET">
    {{ multiple_form }}
    <input type="submit" value="Get Pizzas">
</form>
```
- add a url path named 'pizzas':
```py
path('pizzas', views.pizzas, name='pizzas'),
```
- Go to the forms.py and create a new class:
```py
class MultiplePizzaForm(forms.Form):
    number = forms.IntegerField(min_value=2, max_value=6)
```
- Than switch to views.py and import MultiplePizzaForm, then modify the order function with:
```py
multiple_form = MultiplePizzaForm()
return render(request, 'pizza/order.html', {'pizzaform':new_form, 'note':note, 'multiple_form':multiple_form})
return render(request, 'pizza/order.html', {'pizzaform':form, 'multiple_form':multiple_form})
```
- Next step create a function for the pizza view:
```py
from django.forms import formset_factory
def pizzas(request):
    number_of_pizzas = 2
    filled_multiple_pizza_form = MultiplePizzaForm(request.GET)
    if filled_multiple_pizza_form.is_valid():
        number_of_pizzas = filled_multiple_pizza_form.cleaned_data['number']
    PizzaFormSet = formset_factory(PizzaForm, extra=number_of_pizzas)
    formset = PizzaFormSet()
    if request.method == "POST":
        filled_formset = PizzaFormSet(request.POST)
        if(filled_formset.is_valid()):
            for form in filled_formset:
                print(form.cleaned_data['topping1'])
            note = 'Pizzas have been ordered!'
        else:
            note = 'Order was not created, please try again'


        return render(request, 'pizza/pizzas.html', {'note':note, 'formset':formset})
    else:
        return render(request, 'pizza/pizzas.html', {'formset':formset})
```
- Now we need a new template of pizza.html
```html
<h1>Order Pizzas</h1>


<h2>{{ note }}</h2>


<form action="{% url 'pizzas' %}" method="POST">
  {% csrf_token %}
    {{ formset.management_form }}


    {% for form in formset %}
      {{ form }}
      <br><br>
    {% endfor %}
    <input type="submit" value="Order Pizzas" />
  </form>
```
- Now we have a multiple pizza order page. 
### Save the orders to the Database
- Add a small piece of code to views.py
```py
filled_form.save()
```
- Check if its saving the order or not by typing an order and looking to the admin panel.
- User may need to edit the order.