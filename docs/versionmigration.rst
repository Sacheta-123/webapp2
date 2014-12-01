Version Migration
=================

Migrating from 0.10.X to 1.2.X
------------------------------

This new version has some breaking features. You don't have to change any code, main breaking changes are:

 - The security models schema have changed.

    If you are using sqlite, mysql or pgsql, use the following procedure:

        1 - *Backup your DB*.

        2 - If you haven't already, upgrade to flask-appbuilder 1.2.0.

        3 - Issue the following commands, on your project folder where config.py exists::

            cd /your-main-project-folder/
            wget https://raw.github.com/dpgaspar/Flask-AppBuilder/master/bin/migrate_db_1.2.py
            python migrate_db_1.2.py

        4 - Test and Run (if you have a run.py for development) ::

            python run.py

    If not (DB is not sqlite, mysql or pgsql), you will have to alter the schema your self. use the following procedure:

        1 - *Backup your DB*.

        2 - If you haven't already, upgrade to flask-appbuilder 0.7.0.

        3 - issue the corresponding DDL commands to:

        ALTER TABLE ab_user MODIFY COLUMN password VARCHAR(256)

        ALTER TABLE ab_user ADD COLUMN login_count INTEGER

        ALTER TABLE ab_user ADD COLUMN created_on DATETIME

        ALTER TABLE ab_user ADD COLUMN changed_on DATETIME

        ALTER TABLE ab_user ADD COLUMN created_by_fk INTEGER

        ALTER TABLE ab_user ADD COLUMN changed_by_fk INTEGER

        ALTER TABLE ab_user ADD COLUMN last_login DATETIME

        ALTER TABLE ab_user ADD COLUMN fail_login_count INTEGER

 - Please *backup* your DB before altering the schema,  if you feel lost please post an issue on github
    https://github.com/dpgaspar/Flask-AppBuilder/issues?state=open



Migrating from 0.9.X to 0.10.X
------------------------------

This new version has NO breaking features, all your code will work, unless you are hacking directly onto SQLAModel,
Filters, DataModel etc.

But, to keep up with the changes, you should change these:

::

    from flask.ext.appbuilder.models.datamodel import SQLAModel
    from flask.ext.appbuilder.models.filters import FilterEqual, FilterContains
to::

    from flask.ext.appbuilder.models.sqla.interface import SQLAInterface
    from flask.ext.appbuilder.models.sqla.filters import FilterEqual, FilterContains



Migrating from 0.8.X to 0.9.X
-----------------------------

This new version has a breaking feature, the way you initialize AppBuilder (former BaseApp) has changed.
internal retro compatibility was created, but many things have changed

1 - Initialization of AppBuilder (BaseApp) has changed, pass session not SQLAlchemy *db* object.
this is the breaking feature.

    from (__init__.py) ::

        BaseApp(app, db)

    to (__init__.py) ::

        AppBuilder(app, db.session)


2 - 'BaseApp' changed to 'AppBuilder'. Has you already noticed on 1.

3 - BaseApp or now AppBuilder will not automatically create your models, after declaring them just invoke create_db method::

    appbuilder.create_db()

4 - Change your models inheritance

    from::

        class MyModel(Model):
            id = Column(Integer, primary_key=True)
            first_name = Column(String(64), nullable=False)

    to::

        class MyModel(Model):
            id = Column(Integer, primary_key=True)
            first_name = Column(String(64), nullable=False)

5 - Although your not obligated, you should not directly use your flask.ext.sqlalchemy class SQLAlchemy.
Use F.A.B. SQLA class instead, read the docs to know why.

    from (__init__.py)::

        from flask import Flask
        from flask.ext.sqlalchemy import SQLAlchemy
        from flask.ext.appbuilder.baseapp import BaseApp


        app = Flask(__name__)
        app.config.from_object('config')
        db = SQLAlchemy(app)
        baseapp = BaseApp(app, db)

    to (__init__.py)::

        from flask import Flask
        from flask.ext.appbuilder import SQLA, AppBuilder

        app = Flask(__name__)
        app.config.from_object('config')
        db = SQLA(app)
        appbuilder = AppBuilder(app, db.session)



Migrating from 0.6.X to 0.7.X
-----------------------------

This new version has some breaking features. You don't have to change any code, main breaking changes are:

 - The security models schema have changed.

    If you are using sqlite, mysql or pgsql, use the following procedure:

        1 - *Backup your DB*.

        2 - If you haven't already, upgrade to flask-appbuilder 0.7.0.

        3 - Issue the following commands, on your project folder where config.py exists::

            cd /your-main-project-folder/
            wget https://raw.github.com/dpgaspar/Flask-AppBuilder/master/bin/migrate_db_0.7.py
            python migrate_db_0.7.py
            wget https://raw.github.com/dpgaspar/Flask-AppBuilder/master/bin/hash_db_password.py
            python hash_db_password.py

        4 - Test and Run (if you have a run.py for development) ::

            python run.py

    If not (DB is not sqlite, mysql or pgsql), you will have to alter the schema your self. use the following procedure:

        1 - *Backup your DB*.

        2 - If you haven't already, upgrade to flask-appbuilder 0.7.0.

        3 - issue the corresponding DDL commands to:

        ALTER TABLE ab_user MODIFY COLUMN password VARCHAR(256)

        ALTER TABLE ab_user ADD COLUMN login_count INTEGER

        ALTER TABLE ab_user ADD COLUMN created_on DATETIME

        ALTER TABLE ab_user ADD COLUMN changed_on DATETIME

        ALTER TABLE ab_user ADD COLUMN created_by_fk INTEGER

        ALTER TABLE ab_user ADD COLUMN changed_by_fk INTEGER

        ALTER TABLE ab_user ADD COLUMN last_login DATETIME

        ALTER TABLE ab_user ADD COLUMN fail_login_count INTEGER

        4 - Then hash your passwords::

            wget https://raw.github.com/dpgaspar/Flask-AppBuilder/master/bin/hash_db_password.py
            python hash_db_password.py

 - All passwords are kept on the database hashed, so all your passwords will be hashed by the framework.

 - Please *backup* your DB before altering the schema,  if you feel lost please post an issue on github
    https://github.com/dpgaspar/Flask-AppBuilder/issues?state=open


Migrating from 0.5.X to 0.6.X
-----------------------------

This new version has some breaking features, that i hope will be easily changeable on your code.

If you feel lost please post an issue on github: https://github.com/dpgaspar/Flask-AppBuilder/issues?state=open

If your using the **related_views** attribute on ModelView classes, you must not instantiate the related classes. This is the correct form, it will be less memory and cpu resource consuming.

From this::


    class MyView(GeneralView):
        datamodel = SQLAModel(Group, db.session)
        related_views = [MyOtherView()]


Change to this::

  
    class MyView(GeneralView):
        datamodel = SQLAModel(Group, db.session)
        related_views = [MyOtherView]


Migrating from 0.2.X to 0.3.X
-----------------------------

This new version (0.3.X) has many internal changes, if you feel lost please post an issue on github
https://github.com/dpgaspar/Flask-AppBuilder/issues?state=open

All direct imports from your 'app' directory were removed, so there is no obligation in using the base AppBuilder-Skeleton.

Security tables have changed their names, AppBuilder will automatically migrate all your data to the new tables.

1 - Change your BaseApp initialization (views.py)

From this::

	baseapp = BaseApp(app)

Change to this::

	baseapp = BaseApp(app, db)
	
2 - Remove from OpenID and Login initialization (__init__.py)

From this::

	app = Flask(__name__)
	app.config.from_object('config')
	db = SQLAlchemy(app)
	babel = Babel(app)
	lm = LoginManager()
	lm.init_app(app)
	lm.login_view = 'login'
	oid = OpenID(app, os.path.join(basedir, 'tmp'))
	
	from app import models, views
	
Change to this::

	app = Flask(__name__)
	app.config.from_object('config')
	db = SQLAlchemy(app)
	
	from app import models, views


Migrating from 0.1.X to 0.2.X
-----------------------------

It's very simple, change this::

	baseapp = BaseApp(app)
	baseapp.add_view(GroupGeneralView, "List Groups","/groups/list","th-large","Contacts")
	baseapp.add_view(PersonGeneralView, "List Contacts","/persons/list","earphone","Contacts")
	baseapp.add_view(PersonChartView, "Contacts Chart","/persons/chart","earphone","Contacts")
	
To this::

	baseapp = BaseApp(app)
	baseapp.add_view(GroupGeneralView(), "List Groups","/groups/list","th-large","Contacts")
	baseapp.add_view(PersonGeneralView(), "List Contacts","/persons/list","earphone","Contacts")
	baseapp.add_view(PersonChartView(), "Contacts Chart","/persons/chart","earphone","Contacts")

Small change you just have to instantiate your classes.


