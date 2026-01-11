# django-api
Django pyhton docker api project


# To run tests:
docker compose run --rm app sh -c "python manage.py test"

# To make the create migrations
docker compose run --rm app sh -c "python manage.py make makemigrations" 
docker compose run --rm app sh -c "python manage.py make migrate" 