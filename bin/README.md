Some useful info about the guacamole database.

    guacamole_db=# \d
    List of relations
    Schema |                        Name                        |   Type   |  Owner
    --------+----------------------------------------------------+----------+----------
    public | guacamole_connection                               | table    | postgres
    public | guacamole_connection_connection_id_seq             | sequence | postgres
    public | guacamole_connection_group                         | table    | postgres
    public | guacamole_connection_group_connection_group_id_seq | sequence | postgres
    public | guacamole_connection_group_permission              | table    | postgres
    public | guacamole_connection_history                       | table    | postgres
    public | guacamole_connection_history_history_id_seq        | sequence | postgres
    public | guacamole_connection_parameter                     | table    | postgres
    public | guacamole_connection_permission                    | table    | postgres
    public | guacamole_system_permission                        | table    | postgres
    public | guacamole_user                                     | table    | postgres
    public | guacamole_user_permission                          | table    | postgres
    public | guacamole_user_user_id_seq                         | sequence | postgres
    (13 rows)


    guacamole_db=# \d guacamole_connection
    Table "public.guacamole_connection"
             Column          |          Type          |                                  Modifiers
    --------------------------+------------------------+------------------------------------------------------------------------------
    connection_id            | integer                | not null default nextval('guacamole_connection_connection_id_seq'::regclass)
    connection_name          | character varying(128) | not null
    parent_id                | integer                |
    protocol                 | character varying(32)  | not null
    max_connections          | integer                |
    max_connections_per_user | integer                |
    Indexes:
      "guacamole_connection_pkey" PRIMARY KEY, btree (connection_id)
      "connection_name_parent" UNIQUE CONSTRAINT, btree (connection_name, parent_id)
      "guacamole_connection_parent_id_idx" btree (parent_id)
    Foreign-key constraints:
      "guacamole_connection_ibfk_1" FOREIGN KEY (parent_id) REFERENCES guacamole_connection_group(connection_group_id) ON DELETE CASCADE
    Referenced by:
      TABLE "guacamole_connection_history" CONSTRAINT "guacamole_connection_history_ibfk_2" FOREIGN KEY (connection_id) REFERENCES guacamole_connection(connection_id) ON DELETE CASCADE
      TABLE "guacamole_connection_parameter" CONSTRAINT "guacamole_connection_parameter_ibfk_1" FOREIGN KEY (connection_id) REFERENCES guacamole_connection(connection_id) ON DELETE CASCADE
      TABLE "guacamole_connection_permission" CONSTRAINT "guacamole_connection_permission_ibfk_1" FOREIGN KEY (connection_id) REFERENCES guacamole_connection(connection_id) ON DELETE CASCADE
 
    guacamole_db=# \d guacamole_connection_group
    Table "public.guacamole_connection_group"
              Column          |              Type               |                                        Modifiers
    --------------------------+---------------------------------+------------------------------------------------------------------------------------------
     connection_group_id      | integer                         | not null default nextval('guacamole_connection_group_connection_group_id_seq'::regclass)
     parent_id                | integer                         |
     connection_group_name    | character varying(128)          | not null
     type                     | guacamole_connection_group_type | not null default 'ORGANIZATIONAL'::guacamole_connection_group_type
     max_connections          | integer                         |
     max_connections_per_user | integer                         |
     Indexes:
      "guacamole_connection_group_pkey" PRIMARY KEY, btree (connection_group_id)
      "connection_group_name_parent" UNIQUE CONSTRAINT, btree (connection_group_name, parent_id)
      "guacamole_connection_group_parent_id_idx" btree (parent_id)
      Foreign-key constraints:
      "guacamole_connection_group_ibfk_1" FOREIGN KEY (parent_id) REFERENCES guacamole_connection_group(connection_group_id) ON DELETE CASCADE
    Referenced by:
      TABLE "guacamole_connection_group" CONSTRAINT "guacamole_connection_group_ibfk_1" FOREIGN KEY (parent_id) REFERENCES guacamole_connection_group(connection_group_id) ON DELETE CASCADE
      TABLE "guacamole_connection_group_permission" CONSTRAINT "guacamole_connection_group_permission_ibfk_1" FOREIGN KEY (connection_group_id) REFERENCES guacamole_connection_group(connection_group_id) ON DELETE CASCADE
      TABLE "guacamole_connection" CONSTRAINT "guacamole_connection_ibfk_1" FOREIGN KEY (parent_id) REFERENCES guacamole_connection_group(connection_group_id) ON DELETE CASCADE
    

    guacamole_db=# \d guacamole_user
    Table "public.guacamole_user"
          Column        |          Type          |                            Modifiers
    ---------------------+------------------------+------------------------------------------------------------------
    user_id             | integer                | not null default nextval('guacamole_user_user_id_seq'::regclass)
    username            | character varying(128) | not null
    password_hash       | bytea                  | not null
    password_salt       | bytea                  |
    disabled            | boolean                | not null default false
    expired             | boolean                | not null default false
    access_window_start | time without time zone |
    access_window_end   | time without time zone |
    valid_from          | date                   |
    valid_until         | date                   |
    timezone            | character varying(64)  |
    Indexes:
    "guacamole_user_pkey" PRIMARY KEY, btree (user_id)
    "username" UNIQUE CONSTRAINT, btree (username)
    Referenced by:
    TABLE "guacamole_connection_group_permission" CONSTRAINT "guacamole_connection_group_permission_ibfk_2" FOREIGN KEY (user_id) REFERENCES guacamole_user(user_id) ON DELETE CASCADE
    TABLE "guacamole_connection_history" CONSTRAINT "guacamole_connection_history_ibfk_1" FOREIGN KEY (user_id) REFERENCES guacamole_user(user_id) ON DELETE CASCADE
    TABLE "guacamole_connection_permission" CONSTRAINT "guacamole_connection_permission_ibfk_2" FOREIGN KEY (user_id) REFERENCES guacamole_user(user_id) ON DELETE CASCADE
    TABLE "guacamole_system_permission" CONSTRAINT "guacamole_system_permission_ibfk_1" FOREIGN KEY (user_id) REFERENCES guacamole_user(user_id) ON DELETE CASCADE
    TABLE "guacamole_user_permission" CONSTRAINT "guacamole_user_permission_ibfk_1" FOREIGN KEY (affected_user_id) REFERENCES guacamole_user(user_id) ON DELETE CASCADE
    TABLE "guacamole_user_permission" CONSTRAINT "guacamole_user_permission_ibfk_2" FOREIGN KEY (user_id) REFERENCES guacamole_user(user_id) ON DELETE CASCADE
    
