
# BigBlueButton API  Wrapper for Laravel 
This is a laravel wrapper for BigBlueButton API
## Requirements

- Laravel 5.5

## Installation

Require package in your composer.json and update composer.  This downloads the package and the official bigbluebutton php library. 
```
composer require nguyenducdn87/bigbluebutton
```

## Usage

You can define Big blue button secret key and server url in two ways. 
1. Define in .env file

 ```BBB_SECURITY_SALT =bbb_secret_key```  
 ```BBB_SERVER_BASE_URL=https://example.com/bigbluebutton/``` 
 
 2. Define in config/bigbluebutton.php
 
```
 'BBB_SECURITY_SALT' => 'bbb_secret_key',
 'BBB_SERVER_BASE_URL' => 'https://example.com/bigbluebutton/',
```

## Via Dependency Injection in Controller
**List all meetings**
```php
namespace App\Http\Controllers;
class MeetingController extends Controller
{
    /**
     * @var \Abiodunjames\Bigbluebutton\Contracts\Meeting
     */
    protected $meeting;

    public function __construct(Meeting $meeting)
    {
        $this->meeting = $meeting;
    }
    /**
     *  Returns a list of meetings
     */
    public function all()
    {
        $meetings = $this->meeting->all();
        if ($meetings) {
            // do something with meetings
        }
    }

```
**Create meeting**
```php
use Abiodunjames\Bigbluebutton\Contracts\Meeting;
use BigBlueButton\Parameters\CreateMeetingParameters;
use Illuminate\Http\Request;

class MeetingController extends Controller
{
    /**
     * @var \Abiodunjames\Bigbluebutton\Contracts\Meeting
     */
    protected $meeting;

    public function __construct(Meeting $meeting)
    {
        $this->meeting = $meeting;
    }

    /**
         * Create a bigbluebutton meeting
         *
         * @param \Illuminate\Http\Request $request
         * @return void
         */
        public function create(Request $request)
        {
            $meetingParams = new CreateMeetingParameters($request->meetingId, $request->meetingName);
            $meetingParams->setDuration(40);
            $meetingParams->setModeratorPassword('supersecretpwd');
    
            if ($this->meeting->create($meetingParams)) {
                // Meeting was created
            }
        }

```
**Join a meeting**
```php
use Abiodunjames\Bigbluebutton\Contracts\Meeting;
use BigBlueButton\Parameters\JoinMeetingParameters;
use Illuminate\Http\Request;

class MeetingController extends Controller
{
    /**
     * @var \Abiodunjames\Bigbluebutton\Contracts\Meeting
     */
    protected $meeting;

    public function __construct(Meeting $meeting)
    {
        $this->meeting = $meeting;
    }
    /**
     *  Join a bigbluebutton meeting
     *
     * @param \Illuminate\Http\Request $request
     * @return void
     */
    public function join(Request $request)
    {
        $meetingParams = new JoinMeetingParameters($request->meetingID, $request->meetingName, 'MyMeetingPassword');
        $meetingParams->setRedirect(true);
        $meetingUrl = $this->meeting->join($meetingParams);
        redirect()->setTargetUrl($meetingUrl);
    }

}

```
**Close meeting**
```php

use Abiodunjames\Bigbluebutton\Contracts\Meeting;
use BigBlueButton\Parameters\EndMeetingParameters;
use Illuminate\Http\Request;

class MeetingController extends Controller
{
    /**
     * @var \Abiodunjames\Bigbluebutton\Contracts\Meeting
     */
    protected $meeting;

    public function __construct(Meeting $meeting)
    {
        $this->meeting = $meeting;
    }
    
    /**
     * End a bigbuebutton meeting
     *
     * @param \Illuminate\Http\Request $request
     * @return void
     */
    public function close(Request $request)
    {
        $meetingParams = new EndMeetingParameters($request->meetingID, $request->moderator_password);
        $this->meeting->close($meetingParams);
    }
}

```
## Via Laravel Facade
You can also manage meetings using the facade
```php
Meeting::all(); //get a list of all meetings
```
