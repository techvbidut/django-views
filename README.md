# Django Views

- Django views are **Python functions** that takes web requests and returns web response.
- The response can be the HTML contents of a web page, or a redirect, or a 404 error, or an XML document, or an image, or a JSON response etc.
- By convention, we write our logic inside a `views.py` file present in the app's directory.



### An example of simple view

```
from django.http import HttpResponse
import datetime

def current_datetime(request):
    now = datetime.datetime.now()
    html = "<html><body>It is now %s.</body></html>" % now
    return HttpResponse(html)
```

- The above code is written inside `views.py` file that lives in your app directory.
- It simple returns a HTTP response with the HTML text which shows the current datetime.
- We have used the `HttpResponse` class which lives in the `django.http` module.


#### Code to handle http GET request (Function-based view)
```
from django.http import HttpResponse

def my_view(request):
    if request.method == 'GET':
        # <view logic>
        return HttpResponse('result')
```


#### Equivalent Code to handle http GET request (Class-based view)
```
from django.http import HttpResponse
from django.views import View

class MyView(View):
    def get(self, request):
        # <view logic>
        return HttpResponse('result')
```

You must be wondering how the `urls.py` page will look like:
```
# urls.py
from django.urls import path
from myapp.views import MyView

urlpatterns = [
    path('about/', MyView.as_view()),
]
```

- All the above code snippets are purely based on core django. However, in general django is used as a backend service only, i.e., we user django rest framework commonly known as DRF, to design and develop our APIs or Views. 
- So, we will now look into different types of Views in DRF.
- DRF provides a set of simple decorators that wrap your function based views to ensure they receive an instance of Request (rather than the usual Django HttpRequest) and allows them to return a Response (instead of a Django HttpResponse), and allow you to configure how the request is processed.


## Function-based views in DRF

The core of this functionality is the api_view decorator, which takes a list of HTTP methods that your view should respond to.
A simple function based view in DRF.
```
from rest_framework.decorators import api_view
from rest_framework.response import Response

@api_view()
def hello_world(request):
    return Response({"message": "Hello, world!"})
```

- By default api_view decorator accepts GET method and throws error for other. However, you can change the default setting by adding method names as a param in the decorator, like `@api_view(['GET', 'POST'])`

## Class-based views in DRF

REST framework provides an `APIView` class, which subclasses Django's View class.

```
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import authentication, permissions
from django.contrib.auth.models import User

class ListUsers(APIView):
    """
    View to list all users in the system.

    * Requires token authentication.
    * Only admin users are able to access this view.
    """
    authentication_classes = [authentication.TokenAuthentication]
    permission_classes = [permissions.IsAdminUser]

    def get(self, request, format=None):
        """
        Return a list of all users.
        """
        usernames = [user.username for user in User.objects.all()]
        return Response(usernames)
```


- These were some basic concepts of views in django. However, since there are lot of other topics in views which is out of the scope of this blog, I will just mention out the topics for further reference: Generic views, Viewsets, Mixins etc. 
- All these view modifications were done to make our work easy and reduce the lines of codes to be written. If you understand the basics of simple views, learning complex views woould not be a problem.
- More the ease of use of views, more restricted you are in controlling the flow. However, you can modify it to make changes according to you need. 
- The use of a particular view is completely dependent on the use case and the complexity of your project. However, I prefer to user simple APIView of DRF. Below is the basic layout of how I write my views file.


```
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

class BookView(APIView):
    def get(self, request):
        """ Read/List of Books """
        try:
            #your business logic
        except Exception as e:
            return Response({"error": str(e)}, status=status.HTTP_400_BAD_REQUEST)

        return Response(serializer.data, status=status.HTTP_200_OK)

    def post(self, request):
        """ Create a Book """
        pass

    def patch(self, request):
        """ Update a Book """
        pass

    def delete(self, request):
        """ Delete Book """
        pass
```









