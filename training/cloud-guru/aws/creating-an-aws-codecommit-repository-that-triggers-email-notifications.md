# Creating an AWS CodeCommit Repository That Triggers Email Notifications

## Create a CodeCommit Repository

Navigate to _CodeCommit_ and create a repository.



<figure><img src="../../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

Give repository name a name and click create.

<figure><img src="../../../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

## Create an Amazon SNS Topic

Navigate to _Amazon Simple Notification Service_ and create a topic. Click 'Next Step'. Keep all values to the default.

<figure><img src="../../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

## Subscribe to the Topic

Create a subscription using the _Email_ protocol and enter your email address. **Note:** You will need to confirm your subscription, so make sure you have access to the email you provide.

<figure><img src="../../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

## Create an Event

Create an _Event_ in _EventBridge_ to monitor for changes to AWS CodeCommit.

<figure><img src="../../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

Fill this part out and then click next until the rule is created.

<figure><img src="../../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>



## Test the Event

Return to _CodeCommit_ and upload a file to the repository. Check your email to ensure you've received a notification.

<figure><img src="../../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>



Email sent.

<figure><img src="../../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>
