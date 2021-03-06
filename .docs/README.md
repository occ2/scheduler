# Contributte Scheduler

Executing php callbacks using cron expression.

## Content

- [Setup](#setup)
- [Configuration](#configuration)
- [Jobs](#jobs)
- [Commands](#commands)

## Setup

Require package

```bash
composer require contributte/scheduler
```

Register extension

```yaml
extensions:
    scheduler: Contributte\Scheduler\DI\SchedulerExtension
```

## Configuration

Set-up crontab. Use the `scheduler:run` command.

```
* * * * * php path-to-project/console scheduler:run
```

Optionally, you can set a temp path for storing lock files.

```yaml
scheduler:
    path: '%tempDir%/scheduler'
```

## Jobs

### Callback job

Set cron expression and php callback.

```yaml
services:
    foo: App\Model\Foo

scheduler:
    jobs:
        - cron: '* * * * *'
          callback: [@foo, echo]
        - cron: '*/2 * * * *'
          callback: App\Model\Bar::echo
```

Cron expression:

    *    *    *    *    *
    -    -    -    -    -
    |    |    |    |    |
    |    |    |    |    |
    |    |    |    |    +----- day of week (0 - 7) (Sunday=0 or 7)
    |    |    |    +---------- month (1 - 12)
    |    |    +--------------- day of month (1 - 31)
    |    +-------------------- hour (0 - 23)
    +------------------------- min (0 - 59)

### Custom job

Use the `IJob` interface. Every job is registered as a service in the DIC, so you can use other services.

```php
use Contributte\Scheduler\IJob;

class MyAwesomeJob implements IJob
{

	public function isDue(DateTime $dateTime): bool
	{
		if ($jobIsReadyToRun) {
			return true;
		}
		return false;
	}

	public function run(): void
	{
		// Do something
	}

}

```

And don't forget to register it.

```yaml
scheduler:
    jobs:
        - App\Model\MyAwesomeJob
        myOtherJob: App\Model\MyOtherJob
```

## Commands

### Help

Print cron syntax.

```bash
scheduler:help
```

### List

List all jobs.

```bash
scheduler:list
```

### Run

Run all due jobs.

```bash
scheduler:run
```

