# bin/cron
This command is mainly a wrapper for `bin/magento` for system cron context. It also supports external commands.

## Features
### Logging
The output of the cron command is logged in a file. Different files per passed `identifier` are created. A timestamp
followed by the command output (or `skipped` - see next section) is logged.

### Locking
`flock` ([man page](https://linux.die.net/man/1/flock)) is used to prevent, that a command with the same `identifier`
is run in parallel. The second command will be skipped.

### Commands
The main purpose is to execute `bin/magento` commands. With the `--external` flag other commands can be passed.   

## Installation
### Requirements
For locking, `flock` ([man page](https://linux.die.net/man/1/flock)) needs to be available

### Composer installation
```bash
composer require magenerds/bin-cron
```

For a more comfortable usage of this command, consider adding the executable flag to this command:
```bash
chmod 755 bin/cron
```

## Usage
```
bin/cron [-h|-e|-l] identifier command

-h|--help              Display this help message
-l|--lock              Prevent parallel execution of a <command> with the same <identifier>.
-e|--external          If set, the argument <command> holds a complete command. Otherwise the <command> is passed
                       to bin/magento.
identifier             Used for log file and lock file name
command                bin/magento command or external command if -e flag is passed.
```

## Example
```crontab
# min   hour    day     month   weekday     bin/cron              arguments   identifier          command
*       *       *       *       *           sh <basedir>/bin/cron             group-default       'cron:run --group default'
*       *       *       *       *           sh <basedir>/bin/cron             group-catalog_event 'cron:run --group catalog_event'
*       *       *       *       *           sh <basedir>/bin/cron --lock      group-staging       'cron:run --group staging'
0       1       *       *       *           sh <basedir>/bin/cron --external  foo                 'sh ~/bar/foo.sh'
```