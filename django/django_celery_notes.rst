Django Celery Notes
===================

Celery Task Query Alert Script
------------------------------

If you want to receive an email alert if your Celery task queue backed by Redis gets too high, you could add the following to a directory like, ``/user/local/scripts/celery_queue_alert.py``.

.. code-block:: python

    #!/usr/bin/env python

    import subprocess
    import smtplib


    EMAIL_HOST = 'localhost'
    EMAIL_SUBJECT = "WARNING: Your celery task queue is too high"
    EMAIL_TO = "user@gdomain.com"
    EMAIL_FROM = "root@localhost"
    EMAIL_TEXT = "Your celery task queue currently has %(task_queue_count)s tasks in the queue, which is over the max queue count of %(max_queue_count)s."

    out = subprocess.check_output(['redis-cli', 'llen', 'celery'], stderr=subprocess.STDOUT)
    task_queue_count = int(out.strip())
    max_queue_count = 6

    if task_queue_count >= max_queue_count:
        EMAIL_BODY = "\r\n".join((
            "From: %s" % EMAIL_FROM,
            "To: %s" % EMAIL_TO,
            "Subject: %s" % EMAIL_SUBJECT,
            "",
            EMAIL_TEXT % {'task_queue_count': task_queue_count, 'max_queue_count': max_queue_count})
        )
        email = smtplib.SMTP(EMAIL_HOST)
        email.sendmail(EMAIL_FROM, [EMAIL_TO], EMAIL_BODY)
        email.quit()

Then setup a cron job for it by adding the following to ``/etc/cron.d/celery_queue_alert``.

::

    */5 * * * * root /usr/local/scripts/celery_queue_alert.py

