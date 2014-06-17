dbbackup
========

    dbbackup - A simple Perl script for backing up databases.

SYNOPSIS (Version 1.0)
            dbbackup [choice]

DESCRIPTION
    dbbackup is a simple script to backup databases. It relies on database
    tools such as pg_dump and mysql to do the backups. This version (1.0)
    works with postgresql and mysql databases.

    Dumps are made to an intermediate location *X.inprogress* and moved to
    the final location *X.completed* on successfull completion. Mysql dumps
    are to file; postgres dumps use the more flexible directory format.

    A configuration file dbbackup.conf is required. It is searched for in
    /etc, the script directory and the users home directory. This can be
    redefined at the top of the script as necessary. The format of this file
    is given below. Each section of the configuration refers to a single
    database type and host.

    The script creates a hierarchy of lock files as it dumps each database.
    One lockfile is for the script itself, one for each database
    configuration and one for each database dump. This allows other programs
    to identify what backups are in progress and avoid conflicts.

    The script was deliberately written using Perl modules available from a
    standard Perl install. This was to make it more portable between hosting
    services.

  The dbbackup.conf configuration file
   File Format
    *#* to the end of line
        Are comments. These are stripped and never processed.

    Blank lines
        Are ignored.

    "${foo}" or "$foo\W"
        Variables. The value if taken from the key "foo" in the current
        section, the default section or environment variable of the same
        name in that order. If no value is found the empty string is
        substituted.

    key1=value1
        key1 with value1 in the current section. By default values are
        scalars.

    key1=value2
        A key repeated in a section turns the values into a list.

    identifier
        An identifier (no equals sign) starts a new section. key=value pairs
        before the first section header go into the #default section.

    $confdir
        The key confdir is set to the directory this configuration file is
        found in. It can therefore be used as a variable in the
        configuration file.

   Recognized Key Values
    choice=string
    root=$HOME/tmp/backup
        Common root for the backup tree - where logs, output etc should go.

    lockdir=$root/lock
        Directory lock files will be created.

    logdir=$root/log
        Directory logfiles will be created.

    dumpdir=$root/dump
        Directory database dumps will be created.

    db=mysql|psql
        Identify the type of database a section refers to.

    ignore=true|false
    host=mysql.example.com
        The database host to talk to.

    port=port number
        The port to talk to on the specified host. The default port is
        assumed if non is given.

    user=superduperuser
        A database user (role) with necessary backup privileges.

    password=supersecretpassword [db=mysql only]
        The users password.

    dbname=database1
        Specific databases to be backed up. If non are given the database is
        queried for a list and all are backed up.

        Specify multiple times to specify a list of database names.

    pgpassfile=$confdir/pgpass [db=psql only]
        A suitable postgres pgpass file.

   Notes
    Yes you could make this recurse and break things, but then you get to
    keep all of the pieces.

    Variable substitution is performed on the *#default*. section first and
    then the other sections in alphabetical order.

    No you cannot create identifiers starting with a #. They would be
    stripped out as comments. That is why such keys are used for internal
    purposes.

  Mysql Specifics
    Mysql needs a username and password which must be given in the
    dbbackup.conf file. Please make sure the dbbackup.conf file has minimal
    permissions.

  Postgres Specifics
    Postgres does not accept passwords on the command line. These must be
    provided in a separate, correctly permissioned *pgpass* file. By default
    this file is looked for in the same directory as the dbbackup.conf file.

SECURITY
    Mysql needs a username and password to be given in the dbbackup.conf
    file. Please make sure the dbbackup.conf file has minimal permissions.

    dbdump creates files with predictable names. If it is run with elevated
    privileges then this is a security hole. You must protect against this
    by limiting the privileges of the directories where output (lock files,
    logs and backups) are created.

