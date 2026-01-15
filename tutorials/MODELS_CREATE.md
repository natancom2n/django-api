# create a new models tutorial ( user example):
1- Insise of "user" folder, create the file: serializers.py and put:

    """
    Serializers for the user API View.
    """
    from django.contrib.auth import get_user_model
    from rest_framework import serializers


    class UserSerializer(serializers.ModelSerializer):
        """Serializer for the user object."""

        class Meta:
            model = get_user_model()
            fields = ['email', 'password', 'name']
            extra_kwargs = {'password': {'write_only': True, 'min_length': 5}}

        def create(self, validated_data):
            """Create and return a user with encrypted password."""
            return get_user_model().objects.create_user(**validated_data)

2- After we need create the views of get an response these data that recived:
Open the app/user/view.py:

    """
    Views for the user API.
    """
    from rest_framework import generics
    from user.serializers import UserSerializer

    class CreateUserView(generics.CreateAPIView):
        """Create a new user in the system."""
        serializer_class = UserSerializer

3 - Right now, needs create the urls ( rounte ) to our view:
    a) Create the file urls.py inside of user folder:

        """
        URL mappings for the user API.
        """
        from django.urls import path
        from user import views

        app_name = 'user'

        urlpatterns = [
            path('create/', views.CreateUserView.as_view(), name='create'),
        ]

    b) Inside of app/urls.py :

        #Add include to import:
        from django.urls import path, include

        #and after the last path(), need put:
        ,
        path('api/user/', include('user.urls')),
