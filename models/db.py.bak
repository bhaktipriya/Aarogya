# -*- coding: utf-8 -*-

#########################################################################
## This scaffolding model makes your app work on Google App Engine too
## File is released under public domain and you can use without limitations
#########################################################################

## if SSL/HTTPS is properly configured and you want all HTTP requests to
## be redirected to HTTPS, uncomment the line below:
# request.requires_https()

if not request.env.web2py_runtime_gae:
    ## if NOT running on Google App Engine use SQLite or other DB
    db = DAL('sqlite://storage.sqlite')
else:
    ## connect to Google BigTable (optional 'google:datastore://namespace')
    db = DAL('google:datastore')
    ## store sessions and tickets there
    session.connect(request, response, db = db)
    ## or store session in Memcache, Redis, etc.
    ## from gluon.contrib.memdb import MEMDB
    ## from google.appengine.api.memcache import Client
    ## session.connect(request, response, db = MEMDB(Client()))

## by default give a view/generic.extension to all actions from localhost
## none otherwise. a pattern can be 'controller/function.extension'
response.generic_patterns = ['*'] if request.is_local else []
## (optional) optimize handling of static files
# response.optimize_css = 'concat,minify,inline'
# response.optimize_js = 'concat,minify,inline'

#########################################################################
## Here is sample code if you need for
## - email capabilities
## - authentication (registration, login, logout, ... )
## - authorization (role based authorization)
## - services (xml, csv, json, xmlrpc, jsonrpc, amf, rss)
## - old style crud actions
## (more options discussed in gluon/tools.py)
#########################################################################

from gluon.tools import Auth, Crud, Service, PluginManager, prettydate
auth = Auth(db, hmac_key=Auth.get_or_create_key())
crud, service, plugins = Crud(db), Service(), PluginManager()

## create all tables needed by auth if not custom tables
auth.define_tables()

## configure email
mail=auth.settings.mailer
mail.settings.server = 'logging' or 'smtp.gmail.com:587'
mail.settings.sender = 'you@gmail.com'
mail.settings.login = 'username:password'

## configure auth policy
auth.settings.registration_requires_verification = False
auth.settings.registration_requires_approval = False
auth.settings.reset_password_requires_verification = True

## if you need to use OpenID, Facebook, MySpace, Twitter, Linkedin, etc.
## register with janrain.com, write your domain:api_key in private/janrain.key
from gluon.contrib.login_methods.rpx_account import use_janrain
use_janrain(auth,filename='private/janrain.key')

#########################################################################
## Define your tables below (or better in another model file) for example
##
db.define_table('patient',
                Field('first_name', length=128,requires=IS_NOT_EMPTY()),
                Field('last_name', length=128, requires=IS_NOT_EMPTY()),
                Field('dob','date', requires=IS_NOT_EMPTY()),
                Field('sex', 'string',length=1, requires=[IS_NOT_EMPTY(),IS_IN_SET('F','M')]),
                Field('email', length=128, unique=True,requires=IS_EMAIL()),
                Field('phone', 'string',length=10,requires=IS_NOT_EMPTY()),
                Field('bloodgroup', 'string',length=3,requires=IS_NOT_EMPTY()),
                Field('allergies', 'text',requires=IS_NOT_EMPTY()),
                Field('address', length=256, default=''),
                Field('password', 'password', length=512, readable=False, label='Password'),
                Field('UPID', length=10, writable=False, requires=IS_NOT_EMPTY()) ,
                Field('image','upload')
                )

db.define_table('doctor',
                Field('first_name', length=128,requires=IS_NOT_EMPTY()),
                Field('last_name', length=128, requires=IS_NOT_EMPTY()),
                Field('dob','date', requires=IS_NOT_EMPTY()),
                Field('sex', 'string',length=1, requires=[IS_NOT_EMPTY(),IS_IN_SET('F','M')]),
                Field('email', length=128, unique=True,requires=IS_EMAIL()),
                Field('phone', 'string',length=10,requires=IS_NOT_EMPTY()),
                Field('address', length=256, default=''),
                Field('password', 'password', length=512, readable=False, label='Password'),
                Field('UDID', length=10, writable=False, requires=IS_NOT_EMPTY()) ,
                Field('qualifications', 'text', requires=IS_NOT_EMPTY()),
                Field('visiting hours', 'string', requires=IS_NOT_EMPTY()),
                Field('image','upload')
                )

db.define_table('medicines',
                Field('Name','string',length=128,requires=IS_NOT_EMPTY()),
                Field('Dose','string',length=10, requires=IS_NOT_EMPTY()),
                Field('Salts','string',length=128,requires=IS_NOT_EMPTY()),
                Field('Usage','text',requires=IS_NOT_EMPTY()),
                Field('Quantity','integer',requires=IS_NOT_EMPTY()),
                Field('Expiry Date','date',requires=IS_NOT_EMPTY())
                )
db.define_table('EquipmentsChemicals',
                Field('Name','string',length=128,requires=IS_NOT_EMPTY()),
                Field('Usage','text',requires=IS_NOT_EMPTY()),
                Field('Quantity','integer',requires=IS_NOT_EMPTY()),
                Field('Supplier','string', length=128, requires=IS_NOT_EMPTY())
               )
db.define_table('Prescription',
                Field('Name','string',length=128,requires=IS_NOT_EMPTY()),
                Field('Dose','string',length=10, requires=IS_NOT_EMPTY()),
                Field('Duration','string',length=128,requires=IS_NOT_EMPTY()),
                Field('Other details','text'),
                format='%(Name)s %(Dose)s %(Duration)s %(Other details)s'
                )
db.define_table('FollowUpDetails',
                Field('Date','date', requires=IS_NOT_EMPTY()),
                Field('UDID', length=10, writable=False, requires=IS_NOT_EMPTY()) ,
                format='%(Date)s %(UPID)s'
                )

db.define_table('MedicalReports',
                Field('MRID',length=10, requires=IS_NOT_EMPTY()),
                Field('UPID', length=10, writable=False, requires=IS_NOT_EMPTY()) ,
                Field('UDID', length=10, writable=False, requires=IS_NOT_EMPTY()) ,
                Field('Date','date', requires=IS_NOT_EMPTY()),
                Field('ChiefComplaint','text',requires=IS_NOT_EMPTY()),
                Field('CaseHistory','text',requires=IS_NOT_EMPTY()),
                Field('ClinicalFinding','text',requires=IS_NOT_EMPTY()),
                Field('InvestigationsAdvised','text'),
                Field('Treatment','text'),
                Field('Prescription',db.Prescription),
                Field('Follow-up details',db.FollowUpDetails)
               )
