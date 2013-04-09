========================
PHP Level 2 (EDTECH-521)
========================

- Author: Christer Edwards
- Revision: 2013-04-09

=====
Day 1
=====

Overview
========

- Revision Control Tools (git)
- Mail

================
Revision Control
================

Objectives : Revision Control
=============================

- Discuss different source control methods.
- Learn basic workflow for git.
- Apply git to our existing source code.

Brain Storming Revision Control
===============================

How would you keep track of your code changes over the lifetime of your
project?

Manual Snapshots
================

Manually creating backups of files and directories is one method of managing
revisions of your software.

.. code-block:: bash

    cp project project.bak
    cp project project-2013-04-09

Local Version Control (RCS)
===========================

.. image:: /images/local-vcs.png
   :align: center

Centralized Version Control (SVN)
=================================

.. image:: /images/central-vcs.png
   :align: center

Distributed Version Control (git)
=================================

.. image:: /images/distributed-vcs.png
   :align: center

diff
====

.. code-block:: php

  --- a/register.php  2012-07-27 11:58:26.636629381 -0600
  +++ b/register.php  2012-07-27 11:59:11.983291297 -0600
  @@ -1,6 +1,6 @@
   <?php
   $m = '';
   -if (!empty($_POST['submit'])) {
   +if (isset($_POST['submit'])) {

     $valid = true;
     if ($_POST['password1'] != $_POST['password2']) {

Brief Background
================

Linus Torvalds has quipped about the name "git", which is British English slang
for a stupid or unpleasant person: "I'm an egotistical bastard, and I name all
my projects after myself. First Linux, now git."

Design Criteria
===============

- Take CVS as an example of what not to do; if in doubt, make the exact
  opposite decision.
- Support a distributed workflow.
- Very strong safeguards against corruption, either accidental or malicious.
- Very high performance.

Workflow
========

.. image:: /images/local-operations.png
   :align: center

git Cheat Sheet
===============

- Discuss git cheat sheet

Getting Started
===============

.. code-block:: bash

    sudo yum install git-core

Configure git
=============

.. code-block:: bash

    git config --global user.name "Christer Edwards"
    git config --global user.email "christer.edwards@gmail.com"

Initialize Repository
=====================

.. code-block:: bash

    git init

Adding Files
============

.. code-block:: bash

    git add <filename>

Check Status
============

.. code-block:: bash

    git status

Commit Changes
==============

.. code-block:: bash

    git commit <filename>
    git commit -a
    git commit -m "<message>"

View Log
========

.. code-block:: bash

    git log

View Changes
============

.. code-block:: bash

    git diff <filename>

Visualize Changes
=================

.. code-block:: bash

    gitk --all

Git References
==============

- http://progit.org/book/
- http://gitref.org/
- http://www.spheredev.org/wiki/Git_for_the_lazy

Lab : git
=========

 - git checkout the class repositories:
    - PHP-EDTECH-520
    - PHP-EDTECH-521

(Optional)
 - Create a github account
 - Commit your code to github

====
Mail
====

Objectives : Mail
=================

  * Mail Functions
  * MIME Types
  * Attachments / HTML Email

Sending Mail
============

.. code-block:: php

    mail("a_friend@example.com", "My Subject", "Hello, world!");

.. code-block:: php

    $mailaddress = "a_friend@example.com";
    $mailsubject = "My Subject";          
    $mailbody = "Hello, world!";          
    mail($mailaddress, $mailsubject, $mailbody);

The primary function for sending email is mail(), which takes three basic
parameters and one optional one. These parameters are, in order, the email
address to send to, the subject of the message, the body of the message, and
finally, any extra headers you want to include. Note that this functionality
relies on a working email server that you have permission to use: for Unix
machines, this is often Sendmail; Windows machines, you must set up the SMTP
value in your php.ini file.                                                                                                                                     

MIME Types
==========

.. code-block:: php

    print mime_content_type("myfiles.zip");
    print mime_content_type("poppy.jpg");  

The Multipurpose Internet Mail Extensions (MIME) system was designed to allow
the formatting of emails so that they can include files, and it is made up of
several parts. In order to be able to instruct email clients what types of
files are attached, MIME types were created–short, textual descriptions of the
file types that can be recognized by everyone. MIME types are so popular that
they are used across the Web as a whole now, and mayn operating systems rely on
them to decide how to open a file. In emails, attachments are literally copied
into the message as an encoded string, with MIME boundary markers being used to
tell mail readers where each attachment starts and stops.                                                                                                                                        

Easier Mail Sending with PEAR::Mail
===================================

.. code-block:: php

    sudo pear install --alldeps Mail

.. code-block:: php

    include('Mail.php');
    $mail = Mail::factory("mail");
    
    $headers = array("From"=>"me@example.com", "Subject"=>"Test Mail");
    $body = "This is a test!";                                         
    $mail->send("best@friend.com", $headers, $body);                   

The Mail.php file is the PEAR::Mail script, so it needs to be included before
any PEAR::Mail functions are used. Line two creates a default instance of
PEAR::Mail–the parameter mail is passed in so that PEAR::Mail will use PHP's
mail() function to send the email. If you pass in sendmail, it will send direct
via the sendmail program (Unix only).                                                                

Sending Mixed-Type Messages with PEAR::Mail_Mime
================================================

.. code-block:: bash

    sudo pear install Mail_Mime

.. code-block:: php

    include('Mail.php');
    include('Mail/mime.php');
    
    $message = new Mail_mime();
    $text = file_get_contents("mail_text.txt");
    $html = file_get_contents("mail_html.html");

    $message->setTXTBody($text);                
    $message->setHTMLBody($html);               
    $body = $message->get();                    
    $extraheaders = array("From"=>"me@example.com", "Subject"=>"My Subject 7");
    $headers = $message->headers($extraheaders);                               
    
    $mail = Mail::factory("mail");                                             
    $mail->send("best@friend.com", $headers, $body);                           

Now the script includes both PEAR::Mail and PEAR::Mail_Mime, as it takes both
classes to get the full email sent. Also, rather than handling our message as a
text string, the message is an instance of Mail_mime. In the exmaple, the
message is stored in the $message variable. Next, both the plain text and HTML
messages are retrieved from disk using file_get_contents() and stored in $text
and $html, respectively.                                                                                                                                                                                                         

Once we have the content loaded, we can put it into the message using the
setTxtBody() and setHTMLBody() methods of our $message variable. These both
take a string as their only parameter, so just pass in the appropriate return
value from file_get_contents().                                                                                                                                                       

The body for the message, still stored in $body, now comes from the return
value of $message_>get(). This retrieves the full message text to send, and is
a combination of the HTML and text information all encoded for sending over the
Internet. If you want to see how the system works behind the scenes, echo out
$body and have a look through.                                                                    

With the line starting “$extraheaders = ”, things begin to get more
complicated. The PEAR::Mail→send() function takes its headers as an array and,
to accommodate this, PEAR::Mail_Mime also returns its headers as an array. When
sending complex emails, you need to have a special set of headers in there that
tells the mail reader what to expect. So, once you have your content in place,
you just call headers() to get the header information. As you still need to use
the old headers (from, subject, etc.), you can pass into headers() an array of
existing headers, and it will add these to the array it returns.      

Sending Real Attachments
========================

.. code-block:: php

    $message->addAttachment("example.txt");

Using PEAR::Mail_Mime makes it very easy to add attachments to your messages.
Add this line after the call to setHTMLBody():

You will, of course, need to change example.txt to the name of a file in the
same directory as the script. That's all it takes to add an attachment once you
are using PEAR::Mail and PEAR::Mail_Mime.

Lab : Mail
==========

- Install PEAR::Mail and PEAR::Mail_MIME.
- Email form data to yourself.
- Remember to check-in your code changes.

