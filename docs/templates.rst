Templates
=========

F.A.B. uses jinja2, all automatic templates can be overridden entirely or partially.
This way you can add your own html on jinja2 templates.
This can be done before or after defined blocks on the page,
without the need of developing a template from scratch because you just want to add small changes on it.
Next is a quick description on how you can do this

List Templates
--------------

Using the contacts app example, we are going to see how to override or insert jinja2 on specific sections
of F.A.B. list template. Remember that the framework uses templates with generated widgets, this widgets are big
widgets, because they render entire sections of a page.
On list's of records you will have two widgets, the search widget, and the list widget. You will have
a template with the following sections, where you can add your template sections over, before and after
each block:

- List template
    - Block "content"
        - Block "list_search"
            - Search Widget
        - End Block "list_search"
        - Block "list_list"
            - List Widget
        - End Block "list_list"
    - End Block "content"

To insert your template section over a block, say "list_search" just do:

::

    {% extends "appbuilder/general/model/list.html" %}

        {% block list_search scoped %}
            This Text will replace the search widget
        {% endblock %}

To insert your template section after a block do:

::

    {% extends "appbuilder/general/model/list.html" %}

        {% block list_search scoped %}
            {{ super() }}
            This Text will replace the search widget
        {% endblock %}

I guess you get the general ideal, make use of {{ super() }} to render the block's original content.
To use your templates override **list_template** to your templates relative path, on your ModelView's declaration.

If you have your template on ./your_project/app/templates/list_contacts.html

::

    class ContactModelView(ModelView):
        datamodel = SQLAModel(Contact)

        list_template = 'list_contacts.html'


On your template you can do something like this

::

    {% extends "appbuilder/general/model/list.html" %}

    {% block content %}
        Text on top of the page
        {{ super() }}
        {% block list_search scoped %}
            Text before the search section
            {{ super() }}
        {% endblock %}

        {% block list_list scoped %}
            Text before the list
            {{ super() }}
        {% endblock %}
    {% endblock %}

Add Templates
--------------

On this section we will see how to override the add template form.
You will have only one widget, the add form widget. So you will have
a template with the following sections. Where you can add your template sections over, before and after
each block:

- Add template
    - Block "content"
        - Block "add_form"
            - Add Widget
        - End Block "add_form"
    - End Block "content"

To insert your template section before the a block, say "add_form" just create your own template like this:

::

    {% extends "appbuilder/general/model/add.html" %}

        {% block add_form %}
            This Text is before the add form widget
            {{ super() }}
        {% endblock %}

To use your template define you ModelView with **add_template** declaration to your templates relative path

If you have your template on ./your_project/app/templates/add_contacts.html

::

    class ContactModelView(ModelView):
        datamodel = SQLAModel(Contact)

        add_template = 'add_contacts.html'

Edit Templates
--------------

On this section we will see how to override the edit template form.
You will have only one widget the edit form widget, so you will have
a template with the following sections, where you can add your template sections over, before and after
each block:

- Add template
    - Block "content"
        - Block "edit_form"
            - Edit Widget
        - End Block "edit_form"
    - End Block "content"

To insert your template section before the a block, say "edit_form" just create your own template like this:

::

    {% extends "appbuilder/general/model/edit.html" %}

        {% block add_form %}
            This Text is before the add form widget
            {{ super() }}
        {% endblock %}

To use your template define you ModelView with **edit_template** declaration to your templates relative path

If you have your template on ./your_project/app/templates/edit_contacts.html

::

    class ContactModelView(ModelView):
        datamodel = SQLAModel(Contact)

        edit_template = 'edit_contacts.html'


Show Templates
--------------

On this section we will see how to override the show template.
You will have only one widget the show widget, so you will have
a template with the following sections, where you can add your template sections over, before and after
each block:

- Show template
    - Block "content"
        - Block "show_form"
            - Show Widget
        - End Block "show_form"
    - End Block "content"

To insert your template section before the a block, say "show_form" just create your own template like this:

::

    {% extends "appbuilder/general/model/edit.html" %}

        {% block show_form %}
            This Text is before the show widget
            {{ super() }}
        {% endblock %}

To use your template define you ModelView with **edit_template** declaration to your templates relative path

If you have your template on ./your_project/app/templates/edit_contacts.html

::

    class ContactModelView(ModelView):
        datamodel = SQLAModel(Contact)

        edit_template = 'edit_contacts.html'


Edit/Show Cascade Templates
---------------------------

On cascade templates for related views the above rules apply, but you can use an extra block
to insert your template code before, after or over the related view list widget.
For show cascade templates you have the following structure:

- Show template
    - Block "content"
        - Block "show_form"
            - Show Widget
        - End Block "show_form"
        - Block "related_views"
            - Related Views Widgets
        - End Block "related_views"
    - End Block "content"

Library Functions
-----------------

F.A.B. has the following library functions that you can use to render bootstrap 3
components easily. Using them will ease your productivity and help you introduce
new html that shares the same look and feel has the framework.

- Panel component::

    {{ lib.panel_begin("Panel's Title") }}
        Your html goes here
    {{ lib.panel_end() }}

- Accordion (pass your view's name, or something that will serve has an id)::

    {% call lib.accordion_tag(view.__class__.__name__,"Accordion Title", False) %}
        Your HTML goes here
    {% endcall %}

