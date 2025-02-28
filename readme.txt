# Laravel Cron Job Task Scheduling Example

///////////////////////////////////////////////////
Step 1: Install Laravel

To install Laravel , execute the following command.

composer create-project laravel/laravel example-app

///////////////////////////////////////////////////
Step 2: Create a Command

To create a new custom command that will be executed with task scheduling using a cron job, run the following command.

php artisan make:command TestCron --command=test:cron

app/Console/Commands/TestCron.php

<?php
  
namespace App\Console\Commands;
  
use Illuminate\Console\Command;
use Illuminate\Support\Facades\Http;
use App\Models\User;
  
class TestCron extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'test:cron';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Command description';
  
    /**
     * Execute the console command.
     */
    public function handle()
    {
        info("Cron Job running at ". now());
              
        $response = Http::get('https://jsonplaceholder.typicode.com/users');
          
        $users = $response->json();
      
        if (!empty($users)) {
            foreach ($users as $key => $user) {
                if(!User::where('email', $user['email'])->exists() ){
                    User::create([
                        'name' => $user['name'],
                        'email' => $user['email'],
                        'password' => bcrypt('123456789')
                    ]);
                }
            }
        }
    }
}

///////////////////////////////////////////////////
Step 3: Register Task Scheduler

In this step, we'll define our commands in the console.php file along with the scheduled time for running each command. We'll use functions like ->daily(), ->hourly(), etc.

routes/console.php

<?php
  
use Illuminate\Support\Facades\Schedule;
  
Schedule::command('test:cron')->everyFiveMinutes();

///////////////////////////////////////////////////
Step 4: Run Scheduler Command

Now, we'll run the custom create command using the following laravel artisan command.

php artisan schedule:run
After running, the above command you will see an output like this.

storage/logs/laravel.php

[2024-04-10 23:45:03] local.INFO: Cron Job running at 2024-04-10 23:45:03
[2024-04-10 23:50:05] local.INFO: Cron Job running at 2024-04-10 23:50:05
[2024-04-10 23:55:04] local.INFO: Cron Job running at 2024-04-10 23:45:04
To view an overview of your scheduled tasks, use the schedule:list Artisan command.

php artisan schedule:list

///////////////////////////////////////////////////
Laravel 11 Cron Job Setup on Server

In this step, we'll set up a cron job command on the server. If you're using Ubuntu Server, crontab is likely already installed. Run the command below to add a new entry for the cron job.

crontab -e
* * * * * cd /path-to-your-project & php artisan schedule:run >> /dev/null 2>&1





