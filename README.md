# SQLx-CLI
Command-line utility for SQLx, the Rust SQL toolkit
SQLx CLI
SQLx's associated command-line utility for managing databases, migrations, and enabling "offline" mode with sqlx::query!() and friends.

Install
With Rust toolchain
# supports all databases supported by SQLx
$ cargo install sqlx-cli

# only for postgres
$ cargo install sqlx-cli --no-default-features --features postgres

# use vendored OpenSSL (build from source)
$ cargo install sqlx-cli --features openssl-vendored

# use Rustls rather than OpenSSL
$ cargo install sqlx-cli --no-default-features --features rustls
Usage
All commands require that a database url is provided. This can be done either with the --database-url command line option or by setting DATABASE_URL, either in the environment or in a .env file in the current working directory.

For more details, run sqlx <command> --help.

# Postgres
DATABASE_URL=postgres://postgres@localhost/my_database
Create/drop the database at DATABASE_URL
sqlx database create
sqlx database drop
Create and run migrations
$ sqlx migrate add <name>
Creates a new file in migrations/<timestamp>-<name>.sql. Add your database schema changes to this new file.

$ sqlx migrate run
Compares the migration history of the running database against the migrations/ folder and runs any scripts that are still pending.

Reverting Migrations
If you would like to create reversible migrations with corresponding "up" and "down" scripts, you use the -r flag when creating new migrations:

$ sqlx migrate add -r <name>
Creating migrations/20211001154420_<name>.up.sql
Creating migrations/20211001154420_<name>.down.sql
After that, you can run these as above:

$ sqlx migrate run
Applied migrations/20211001154420 <name> (32.517835ms)
And reverts work as well:

$ sqlx migrate revert
Applied 20211001154420/revert <name>
Note: attempting to mix "simple" migrations with reversible migrations with result in an error.

$ sqlx migrate add <name1>
Creating migrations/20211001154420_<name>.sql

$ sqlx migrate add -r <name2>
error: cannot mix reversible migrations with simple migrations. All migrations should be reversible or simple migrations
Enable building in "offline mode" with query!()
Note: must be run as cargo sqlx.

cargo sqlx prepare
Saves query metadata to sqlx-data.json in the current directory; check this file into version control and an active database connection will no longer be needed to build your project.

Has no effect unless the offline feature of sqlx is enabled in your project. Omitting that feature is the most likely cause if you get a sqlx-data.json file that looks like this:

{
    "database": "PostgreSQL"
}
cargo sqlx prepare --check
Exits with a nonzero exit status if the data in sqlx-data.json is out of date with the current database schema and queries in the project. Intended for use in Continuous Integration.

Force building in offline mode
To make sure an accidentally-present DATABASE_URL environment variable or .env file does not result in cargo build (trying to) access the database, you can set the SQLX_OFFLINE environment variable to true.

If you want to make this the default, just add it to your .env file. cargo sqlx prepare will still do the right thing and connect to the database.

Dependencies
~13–21MB
~423K SLoC

anyhow
async-trait
chrono
clap 3.0.0-rc.9+derive+env+cargo
console 0.14.1
dotenv
futures 0.3.19
glob
promptly
remove_dir_all 0.7
serde+derive
serde_json
sqlx+migrate+any+offline
tokio 1.15+macros+rt+rt-multi-thread
url 2.2
openssl-vendored? openssl
