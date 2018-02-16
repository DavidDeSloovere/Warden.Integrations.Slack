# Warden Slack Integration

![Warden](http://spetz.github.io/img/warden_logo.png)

**OPEN SOURCE & CROSS-PLATFORM TOOL FOR SIMPLIFIED MONITORING**

**[getwarden.net](http://getwarden.net)**

|Branch             |Build status                                                  
|-------------------|-----------------------------------------------------
|master             |[![master branch build status](https://api.travis-ci.org/warden-stack/Warden.Integrations.Slack.svg?branch=master)](https://travis-ci.org/warden-stack/Warden.Integrations.Slack)
|develop            |[![develop branch build status](https://api.travis-ci.org/warden-stack/Warden.Integrations.Slack.svg?branch=develop)](https://travis-ci.org/warden-stack/Warden.Integrations.Slack/branches)

**SlackIntegration** can be used for sending messages to the **[Slack](https://slack.com)** using the webhook integration.

### Installation:

Available as a **[NuGet package](https://www.nuget.org/packages/Warden.Integrations.Slack)**. 
```
dotnet add package Warden.Integrations.Slack
```

### Configuration:

 - **WithDefaultMessage()** - default message text.
 - **WithDefaultChannel()** - default name of channel to which the message will be sent.
 - **WithDefaultUsername()** - default username that will send the message.
 - **WithTimeout()** - timeout of the HTTP request to the Slack API.
 - **FailFast()** - flag determining whether an exception should be thrown if _SendMessageAsync()_ returns invalid reponse (false by default).
 - **WithSlackServiceProvider()** -  custom provider for the _ISlackService_.

**SlackIntegration** can be configured by using the **SlackIntegrationConfiguration** class or via the lambda expression passed to a specialized constructor. 

### Initialization:

In order to register and resolve **SlackIntegration** make use of the available extension methods while configuring the **Warden**:

```csharp
var wardenConfiguration = WardenConfiguration
    .Create()
    .IntegrateWithSlack("https://hooks.slack.com/services/XXX/YYY/ZZZ", cfg =>
    {
        cfg.WithDefaultMessage("Monitoring status")
           .WithDefaultUsername("Warden");
    })
    .SetGlobalWatcherHooks((hooks, integrations) =>
    {
        hooks.OnStart(check => GlobalHookOnStart(check))
             .OnFailure(result => integrations.Slack().SendMessageAsync("Monitoring errors have occured."))
    })
    //Configure watchers, hooks etc..
```

### Custom interfaces:
```csharp
public interface ISlackService
{
    Task SendMessageAsync(string message, string channel = null, string username = null, 
            TimeSpan? timeout = null, bool failFast = false);
}
```

**ISlackService** is responsible for sending the message using [Slack Webhook](https://api.slack.com/incoming-webhooks). It can be configured via the *WithSlackServiceProvider()* method. By default, it is based on the HttpClient library.