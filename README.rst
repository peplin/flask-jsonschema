Flask-JsonSchema
================

JSON request validation for Flask applications.

Place schemas in the specified ``JSONSCHEMA_DIR``. ::

    import os

    from flask import Flask, request
    from flask_jsonschema import JsonSchema, ValidationError, \
            validate_schema

    app = Flask(__name__)
    app.config['JSONSCHEMA_DIR'] = os.path.join(app.root_path, 'schemas')

    JsonSchema(app)

    @app.errorhandler(ValidationError)
    def on_validation_error(e):
        return "error"

    @app.route('/books', methods=['POST'])
    @validate_schema('books', 'create')
    def create_book():
        # create the book
        return 'success'

The schema for the example above should be named ``books.json`` and should
reside in the configured folder. It should look like so::

    {
      "create": {
        "type": "object",
        "properties": {
          "title": {},
          "author": {}
        },
        "required": ["title", "author"]
      },
      "update": {
        "type": "object",
        "properties": {
          "title": {},
          "author": {}
        }
      }
    }

Notice the top level action names. Flask-JsonSchema supports one "path" level so
that you can organize related schemas in one file. If you do not wish to use this
feature you can simply use one schema per file and remove the second parameter
to the ``@jsonschema.validate`` call.

Using prmd-style Organization
-----------------------

Heroku's `prmd <https://github.com/interagent/prmd>`_ tool requires a slightly
different organization for your schema. This Flask plugin also supports
validating against the compiled, generated schema file from prmd.

Instead of ``JSONSCHEMA_DIR``, specify ``JSONSCHEMA_SCHEMA`` in your config,
which must point to the single, compiled .json file generated by prmd::

    app.config['JSONSCHEMA_SCHEMA'] = os.path.join(app.root_path, 'schema.json')


When initializing the app, specify the ``PrmdSchemaLoader``::

    JsonSchema(self, loader_class=PrmdSchemaLoader)

Resources
---------

- `Issue Tracker <http://github.com/mattupstate/flask-jsonschema/issues>`_
- `Code <http://github.com/mattupstate/flask-jsonschema/>`_
