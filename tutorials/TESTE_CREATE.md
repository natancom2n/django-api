# create a new app tutorial:
1-
    docker compose run --rm app sh -c "python manage.py startapp name_of_app"
        After run the command above, you need delete the:
        Directories:
            - migrations
            - admin,
        Files:
            - model.py
            - tests.py

        After create the directory /name_of_app/tests/
        Inside this directory  will need create the file __init.py__

2- Go at app/app/settings.py.
    Set in the final of
        INSTALLED_APPS = [

           'name_of_app',
        ]
        OBS.: The use the same name did you create the last app, and the same name of the folder created

3- Create a new tests to new app, I will use how example the "user" app.
    So I ran: docker compose run --rm app sh -c "python manage.py startapp user"
    a) Create the new file inside /user/tests/test_user_api.py and types:
        from django.test import TestCase
        from django.contrib.auth import get_user_model
        from django.urls import reverse

        from rest_framework.test import APIClient
        from rest_framework import status

    b) Create the url to test:
        CREATE_USER_URL = reverse('user:create')

    c) You need create a new user test:

        def create_user(**params):
        """Create and return a new user."""
        return get_user_model().objects.create_user(**params)

    d) So create a new Class to put all kind of test you need:

        class PublicUserApiTests(TestCase):
            """Test the public features of the user API."""

            def setUp(self):
                self.client = APIClient()

    e) Example of test user successful - 201

            def test_create_user_success(self):
            """Test creating a user is successful."""
            payload = {
                'email': 'test@example.com',
                'password': 'testpass123',
                'name': 'Test Name',
            }
            res = self.client.post(CREATE_USER_URL, payload)

            #if the Equals ( result of test == http 201)
            self.assertEqual(res.status_code, status.HTTP_201_CREATED)

            user = get_user_model().objects.get(email=payload['email'])
            self.assertTrue(user.check_password(payload['password']))
            self.assertNotIn('password', res.data)

    f) Another example right now is the test that will be retur ERROR,
      because the email will be wrong form:

            def test_user_with_email_exists_error(self):
            """Test error returned if user with email exists."""
            payload = {
                'email': 'test@example.com',
                'password': 'testpass123',
                'name': 'Test Name',
            }
            create_user(**payload)
            res = self.client.post(CREATE_USER_URL, payload)

            self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)

    g) Example teste the password caractes is not at least 6 :

            def test_password_too_short_error(self):
                """Test an error is returned if password less than 5 chars."""
                payload = {
                    'email': 'test@example.com',
                    'password': 'pw',
                    'name': 'Test name',
                }
                res = self.client.post(CREATE_USER_URL, payload)

                self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)
                user_exists = get_user_model().objects.filter(
                    email=payload['email']
                ).exists()
                self.assertFalse(user_exists)