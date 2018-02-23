---
layout: wp
title: About sqlalchemy
comments: true
---

从[这个 tutorial](http://docs.sqlalchemy.org/en/latest/orm/tutorial.html)，我们来看一下使用 sqlalchemy 的基本用法。   


```
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String


Base = declarative_base()


# Declare a Mapping
class User(Base):
    __tablename__ = 'users'
    id = Column(Integer, primary_key=True)
    name = Column(String(50))
    fullname = Column(String(50))
    password = Column(String(12))

    def __repr__(self):
        return "<User(name='%s', fullname='%s', password='%s')>" % (
                                self.name, self.fullname, self.password)


# Connecting engine
engine = create_engine('sqlite:///:memory:', echo=True)


# Creating a Session
Session = sessionmaker(bind=engine)
session = Session()


# Create a Schema(table)
Base.metadata.create_all(engine)

# Create an Instance of the Mapped Class
ed_user = User(name='ed', fullname='Ed Jones', password='edspassword')



# Adding and Updating Objects
session.add(ed_user)
our_user = session.query(User).filter_by(name='ed').first()
print our_user


ed_user.password = 'f8s7ccs'
session.commit()
our_user = session.query(User).filter_by(name='ed').first()
print our_user
```

我看来看下它的整体架构： 

[![SQLAlchemy 架构](http://docs.sqlalchemy.org/en/latest/core/engines.html)](http://docs.sqlalchemy.org/en/latest/core/engines.html)   



```
├── __init__.py
├── connectors
│   ├── __init__.py
│   ├── mxodbc.py
│   ├── pyodbc.py
│   └── zxJDBC.py
├── cprocessors.so
├── cresultproxy.so
├── cutils.so
├── databases
│   └── __init__.py
├── dialects
│   ├── __init__.py
│   ├── firebird
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── fdb.py
│   │   └── kinterbasdb.py
│   ├── mssql
│   │   ├── __init__.py
│   │   ├── adodbapi.py
│   │   ├── base.py
│   │   ├── information_schema.py
│   │   ├── mxodbc.py
│   │   ├── pymssql.py
│   │   ├── pyodbc.py
│   │   └── zxjdbc.py
│   ├── mysql
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── cymysql.py
│   │   ├── dml.py
│   │   ├── enumerated.py
│   │   ├── gaerdbms.py
│   │   ├── json.py
│   │   ├── mysqlconnector.py
│   │   ├── mysqldb.py
│   │   ├── oursql.py
│   │   ├── pymysql.py
│   │   ├── pyodbc.py
│   │   ├── reflection.py
│   │   ├── types.py
│   │   └── zxjdbc.py
│   ├── oracle
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── cx_oracle.py
│   │   └── zxjdbc.py
│   ├── postgresql
│   │   ├── __init__.py
│   │   ├── array.py
│   │   ├── base.py
│   │   ├── dml.py
│   │   ├── ext.py
│   │   ├── hstore.py
│   │   ├── json.py
│   │   ├── pg8000.py
│   │   ├── psycopg2.py
│   │   ├── psycopg2cffi.py
│   │   ├── pygresql.py
│   │   ├── pypostgresql.py
│   │   ├── ranges.py
│   │   └── zxjdbc.py
│   ├── sqlite
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── pysqlcipher.py
│   │   └── pysqlite.py
│   └── sybase
│       ├── __init__.py
│       ├── base.py
│       ├── mxodbc.py
│       ├── pyodbc.py
│       └── pysybase.py
├── engine
│   ├── __init__.py
│   ├── base.py (engine & connection, Provides high-level functionality for a wrapped DB-API connection)
│   ├── default.py
│   ├── interfaces.py
│   ├── reflection.py
│   ├── result.py
│   ├── strategies.py
│   ├── threadlocal.py
│   ├── url.py
│   └── util.py
├── event
│   ├── __init__.py
│   ├── api.py
│   ├── attr.py
│   ├── base.py
│   ├── legacy.py
│   └── registry.py
├── events.py
├── exc.py
├── ext
│   ├── __init__.py
│   ├── associationproxy.py
│   ├── automap.py
│   ├── baked.py
│   ├── compiler.py
│   ├── declarative
│   │   ├── __init__.py
│   │   ├── api.py
│   │   ├── base.py
│   │   └── clsregistry.py
│   ├── horizontal_shard.py
│   ├── hybrid.py
│   ├── indexable.py
│   ├── instrumentation.py
│   ├── mutable.py
│   ├── orderinglist.py
│   └── serializer.py
├── inspection.py
├── interfaces.py
├── log.py
├── orm
│   ├── __init__.py
│   ├── attributes.py
│   ├── base.py
│   ├── collections.py
│   ├── dependency.py
│   ├── deprecated_interfaces.py
│   ├── descriptor_props.py
│   ├── dynamic.py
│   ├── evaluator.py
│   ├── events.py
│   ├── exc.py
│   ├── identity.py
│   ├── instrumentation.py
│   ├── interfaces.py
│   ├── loading.py (convert database rows into object instances and associated state)
│   ├── mapper.py
│   ├── path_registry.py
│   ├── persistence.py
│   ├── properties.py
│   ├── query.py (central construct useed by the orm to construct database queries,查询类)
│   ├── relationships.py
│   ├── scoping.py
│   ├── session.py (Manages persistence operations for ORM-mapped objects)
│   ├── state.py
│   ├── strategies.py
│   ├── strategy_options.py
│   ├── sync.py
│   ├── unitofwork.py
│   └── util.py
├── pool.py
├── processors.py
├── schema.py
├── sql (sql表达式)
│   ├── __init__.py
│   ├── annotation.py
│   ├── base.py
│   ├── compiler.py
│   ├── crud.py
│   ├── ddl.py
│   ├── default_comparator.py
│   ├── dml.py
│   ├── elements.py
│   ├── expression.py
│   ├── functions.py
│   ├── naming.py
│   ├── operators.py
│   ├── schema.py
│   ├── selectable.py
│   ├── sqltypes.py
│   ├── type_api.py
│   ├── util.py
│   └── visitors.py
├── testing
│   ├── __init__.py
│   ├── assertions.py
│   ├── assertsql.py
│   ├── config.py
│   ├── engines.py
│   ├── entities.py
│   ├── exclusions.py
│   ├── fixtures.py
│   ├── mock.py
│   ├── pickleable.py
│   ├── plugin
│   │   ├── __init__.py
│   │   ├── bootstrap.py
│   │   ├── noseplugin.py
│   │   ├── plugin_base.py
│   │   └── pytestplugin.py
│   ├── profiling.py
│   ├── provision.py
│   ├── replay_fixture.py
│   ├── requirements.py
│   ├── runner.py
│   ├── schema.py
│   ├── suite
│   │   ├── __init__.py
│   │   ├── test_ddl.py
│   │   ├── test_dialect.py
│   │   ├── test_insert.py
│   │   ├── test_reflection.py
│   │   ├── test_results.py
│   │   ├── test_select.py
│   │   ├── test_sequence.py
│   │   ├── test_types.py
│   │   └── test_update_delete.py
│   ├── util.py
│   └── warnings.py
├── types.py
└── util
    ├── __init__.py
    ├── _collections.py
    ├── compat.py
    ├── deprecations.py
    ├── langhelpers.py
    ├── queue.py
    └── topological.py
```


看一下`query`的最终的返回部分：   

```
    def __iter__(self):
        context = self._compile_context()
        context.statement.use_labels = True
        if self._autoflush and not self._populate_existing:
            self.session._autoflush()
        return self._execute_and_instances(context)

    def _execute_and_instances(self, querycontext):
        conn = self._connection_from_session(
                        mapper=self._mapper_zero_or_none(),
                        clause=querycontext.statement,
                        close_with_result=True)

        result = conn.execute(querycontext.statement, self._params)
        return loading.instances(self, result, querycontext)

```


### session connection ###   


```
    def connection(self, mapper=None, clause=None,
                        bind=None,
                        close_with_result=False,
                        **kw):
		if bind is None:
            bind = self.get_bind(mapper, clause=clause, **kw)

        return self._connection_for_bind(bind,
                                        close_with_result=close_with_result)

	def _connection_for_bind(self, engine, **kwargs):
        if self.transaction is not None:
            return self.transaction._connection_for_bind(engine)
        else:
            return engine.contextual_connect(**kwargs)


# connection, engine.default
    def connect(self, *cargs, **cparams):
        return self.dbapi.connect(*cargs, **cparams)

```


### pool ###

```
		pool = kwargs.pop('pool', None)
        if pool is None:
            def connect():
                try:
                    return dialect.connect(*cargs, **cparams)
                except dialect.dbapi.Error, e:
                    invalidated = dialect.is_disconnect(e, None, None)
                    # Py3K
                    #raise exc.DBAPIError.instance(None, None,
                    #    e, dialect.dbapi.Error,
                    #    connection_invalidated=invalidated
                    #) from e
                    # Py2K
                    import sys
                    raise exc.DBAPIError.instance(
                        None, None, e, dialect.dbapi.Error,
                        connection_invalidated=invalidated
                    ), None, sys.exc_info()[2]
                    # end Py2K

            creator = kwargs.pop('creator', connect)

            poolclass = kwargs.pop('poolclass', None)
            if poolclass is None:
                poolclass = dialect_cls.get_pool_class(u)
```



