Working with AWS SQS Standard Queues
Introduction
In this hands-on lab, you will learn how to create and interact with SQS standard queues. You will send messages to an SQS queue that you create and learn how to take advantage of SQS queues to use multiple SQS consumers to process queue data at the same time! By the end of this AWS learning activity, you should feel comfortable interacting with the SQS service via the Boto3 SDK for Python. You will also gain an understanding of how to send messages to standard queues, set queue attributes, and consume messages from the queues we create.

Solution
Open 5 terminal windows and log in to the provided public instance on the lab page via SSH:

ssh cloud_user@<PUBLIC_IP_ADDRESS>
We'll be using each of them at the same time, so you'll want to make sure you know which is which. It could help to change the background color on each window so you can easily tell them apart.

Create a Standard SQS Queue (First Terminal)
In one of the windows, create a queue using the Python script:

python3 create_queue.py
When the command finishes, we'll see the URL of our queue (e.g., https://queue.amazonaws.com/xxxxxxxxxxxx/mynewq). Copy that URL.

Open the sqs_url.py file:

vim sqs_url.py
Update the file so it includes the URL you just copied:

QUEUE_URL = 'https://queue.amazonaws.com/xxxxxxxxxxxx/mynewq'
Save and quit the file by hitting Escape and entering wq!.

View the contents of the file to make sure it's updated:

cat sqs_url.py
Monitor the Queue (Second Terminal)
In another terminal window, run a script that will keep track of what's going on in our queue:

python3 queue_status.py
This will check on messages in our queue, so leave it running in the window. You may want to make this window smaller than the others to keep it out of the way while still keeping an eye on it.

Send Data (Third Terminal)
In a third terminal, run a script that will start pushing data to the queue:

python3 slow_producer.py
We'll see a lot of information start to appear. After a few seconds, we'll also see the second/monitoring terminal window change, with the numbers starting to increase for the different kinds of messages.

Once the slow_producer script stops running (it will take a few minutes, and we should have 50 messages total), run the following script in the third terminal:

python3 fast_producer.py
The difference between the slow_producer and fast_producer is the time they wait between sending messages — slow waits 10 seconds, and fast only waits 1 second.

We'll end up with 100 messages total once it finishes running (it will take about a minute).

Receive Messages and Extract Metadata (Fourth and Fifth Terminals)
In the fourth terminal window, run a script that will receive messages from the queue and extract some metadata from them:

python3 fast_consumer.py
We'll see a lot of data start flowing in the fourth terminal. We'll also see (in the monitoring terminal window) the number next to ApproximateNumberOfMessages start to drop.

No need to wait this time — in the fifth terminal, run the following:

python3 slow_consumer.py
This is just a slower version of the fast_consumer script (as evidenced by its name).

Keep an eye on the monitoring terminal window — we'll see the number of ApproximateNumberOfMessages continue to drop. We now have 2 scripts running that are both snagging messages out of the queue, extracting data, and deleting them.

Clean Up (Any Terminal)
We don't need to wait for the previous 2 scripts to finish. To cancel the processes, hit Ctrl+C in the monitoring window as well as the fourth and fifth terminal windows.

Run the following in any terminal window to remove all the messages from the queue:

python3 purge_queue.py
Run Everything at Once (All Terminals)
Keep the monitoring terminal window there to keep an eye on it while running all of the scripts. (If you need to re-run the command again, it's python3 queue_status.py.)

In the next terminal, run:

python3 slow_producer.py
In the next terminal, run:

python3 slow_consumer.py
In the next terminal, run:

python3 fast_consumer.py
Watch the monitoring terminal window — the number of messages should start going down.

In the next terminal, run:

python3 fast_producer.py
In the first/monitoring terminal, we should see the number of messages start going back up again. This will continue until the producer scripts run out of data to read and the consumer scripts have processed all of the messages.

Eventually, the monitoring terminal window's 3 rows will all have 0 counts.

Conclusion
Congratulations on successfully completing this hands-on lab!