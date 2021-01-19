# Audata Webhooks

Audata has a standardised Ruby DSL for defining and using Webhooks across its Rails-based apps. Webhooks are user-definable events that occur within the application that trigger a HTTPS POST call to an external endpoint (for example when a record is created or updated).

### Defining Webhooks

Webhooks are defined on the ActiveRecord model using the `webhooks_for` method.

```ruby
webhooks_for :created, :some_event # Defines two webhooks for the model
```

### Calling Webhooks

Webhooks are called using the `webhook` method with the action name. Multiple webhooks can be defined on the same line with a single `webhook` method.

### Standard Webhooks

Webhooks can be created automatically for ActiveRecord models on Create, Update, and Destroy events.

```ruby
class Campaign < ApplicationRecord
  # Define webhooks
  webhooks_for :created, :updated, :deleted

end
```

"created", "updated", and "deleted" webhooks will automatically attach themselves to their respective ActiveRecord callbacks.

### Custom Webhooks

Webhooks can also be defined for other events on an ActiveRecord model, then called using `webhook` and the action name as the argument.

#### Defining a Custom Webhook

Custom Webhooks are defined the same way as standard Webhooks. The below example will register a Webhook Event called `Email.was_delivered`...

```ruby
class Email < ApplicationRecord
  # Define webhooks
  webhooks_for :was_delivered
end
```

Unlike standard webhooks, Custom webhooks must be called manually using the `webhook` method.

```ruby
class Email < ApplicationRecord
  def deliver_email!
    EmailSendingJob.perform_later(self)

    # Trigger the webhook
    webhook :was_delivered
  end
end
```

### Calling Webhooks from Controllers

Webhooks can be called from controllers by calling the `webhook` instance method on the sender/object with the action name as the argument.

```ruby
class UsersController < ApplicationController

  def confirm_email
    # Do something here
    @user.webhook :email_confirmed
  end

end
```

### Webhook Event Format

Webhook Events are a string which defines the event that occurs to trigger a Webhook call. Webhook Events are made of two components:

- Model
- Action

Webhook Events have a standardised format which is `ModelName.action_name`. The Model Name should be PascalCase and the action name should be underscore_case.

### Listing Webhooks

Available Webhooks can be listed using

```ruby
Webhook.valid_events
```
