Helios Voting Installation HowTo
================================

We intend to use Helios as a backend to provide the secure e-voting 
funcitonality. The installation instructions at 
http://documentation.heliosvoting.org/install are unfortunately a bit
outdated. (It's been a while since Ubuntu 10.04 and latest versions of 
some of the python modules needed don't really easy_install so easily
anymore on such an old Ubuntu...)

This file is here to fill the gaps.

Note: Like the Helios documentation, we omit the use of "sudo" in
the instructions, but it's really needed for all apt-get, easy_install
and "python setup.py install" steps.


I tested with Ubuntu 12.04 Precise. (Quantal didn't have a working
Vagrant box available yet.)

1) While it's good to use a modern Ubuntu, you really need to use
Django 1.2. So *DON'T DO THIS*:

    apt-get install python-django

Instead install Django 1.2 like this:

    wget http://www.djangoproject.com/m/releases/1.2/Django-1.2.7.tar.gz
    tar xvf Django-1.2.7.tar.gz
    cd Django-1.2.7/
    python setup.py install

2) To use git, you of course first need to install it:

    apt-get install git


3) The "sh ./reset.sh" will fail. You need to jump to the bottom of the 
instructions and install everything from the "Jobs to Run Regularly" part.
(rabbitmq-server, celery, django-celery).

4) When you get to the last line: 

    python manage.py celeryd

It will fail because there's still stuff missing. You need to...

5) Install unicodecsv. To make things even more fun, this currently fails
when using easy_install. So you need to....

    wget http://pypi.python.org/packages/source/u/unicodecsv/unicodecsv-0.9.3.tar.gz
    tar xvf unicodecsv-0.9.3.tar.gz
    cd unicodecsv-0.9.3/
    echo foobar > README.rst
    sudo python setup.py install

6) Install djkombu.

    sudo easy_install django-kombu

7) Initialize the postgresql database schema.

    python manage.py syncdb

8) Now you can finally start celeryd.

    python manage.py celeryd

If it works, you've come a long way. But you still need to shut it down. Hit Ctrl-C.


9) Ok, now you can go backwards in the Helios installation instructions to the place
where the "sh ./reset.sh" command is mentioned.

10) Helios wants to send emails. If you don't have a mail server running, you need to
install it:

    apt-get install postfix


11) Edit settings.py:

    Line 153: For testing it's probably easier with user/password authentication:
    # authentication systems enabled
    AUTH_ENABLED_AUTH_SYSTEMS = ['password','facebook','twitter', 'google', 'yahoo']
    #AUTH_ENABLED_AUTH_SYSTEMS = ['google']
    AUTH_DEFAULT_AUTH_SYSTEM = 'password'

    Line 177: Probably change email port to 25 (not 2525)
    
12) Edit reset.sh. Change the last line to:

    echo "from auth.models import User; User.objects.create(user_type='password',user_id='vagrant@localhost', info={'name':'Sysadmin', 'password':'secret'})" | python manage.py shell

13) You can now start the Helios web server and the celeryd background process as in
the original installation instructions:


    python manage.py celeryd

And

    python manage.py runserver 0.0.0.0:8000


14) Point your browser to localhost:8000 and log in with
username  "vagrant@localhost" and password "secret".
