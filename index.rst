========================
PHP Level 2 (EDTECH-521)
========================

- Author: Christer Edwards
- Revision: 2013-11-05

=====
Day 1
=====

Review
======

- Web Form Design and Development
  (http://freecourses.tutsplus.com)

Overview
========

- Revision Control Tools (git)
- Mail

================
Revision Control
================

Objectives : Revision Control
=============================

- Background / History
- Discuss different source control methods.
- Learn basic workflow for git.
- Apply git to our existing source code.

Brainstorming Revision Control
==============================

How would you keep track of your code changes over the lifetime of your
project?

Manual Snapshots
================

Manually creating backups of files and directories is one method of managing
revisions of your software.

.. code-block:: bash

    cp project project.bak
    cp project project-2013-11-05

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

.. code-block:: diff

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

Tracking revisions
==================

.. image:: /images/git-revisions.png
  :align: center

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

OR

Search for ``git-core`` in the Apper utility

Configure git
=============

.. code-block:: bash

    git config --global user.name "Christer Edwards"
    git config --global user.email "christer.edwards@gmail.com"
    git config --global color.ui true

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

MIME Types
==========

.. code-block:: php

    print mime_content_type("myfiles.zip");
    print mime_content_type("poppy.jpg");  

Easier Mail Sending with PEAR::Mail
===================================

.. code-block:: bash

    sudo pear install --alldeps Mail

.. code-block:: php

    include('Mail.php');
    $mail = Mail::factory("mail");
    
    $headers = array("From"=>"me@example.com", "Subject"=>"Test Mail");
    $body = "This is a test!";                                         
    $mail->send("best@friend.com", $headers, $body);                   

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

Sending Real Attachments
========================

.. code-block:: php

    $message->addAttachment("example.txt");

Lab : Mail
==========

- Install PEAR::Mail and PEAR::Mail_MIME.
- Email form data to yourself.
- Remember to check-in your code changes.

=====
Day 2
=====

Overview
========

  - Files

=====
Files
=====

Files : Objectives
==================

  - Reading, Editing and Creating Files
  - Locking Files                      
  - File Permissions                   
  - Uploading Files

=============
Reading Files
=============

readfile()
==========

.. code-block:: php

    $testfile = @readfile('test.txt');
                                      
    if (!$testfile) {                 
      print "Could not open file\n";  
    }                                 

file_get_contents()
===================

.. code-block:: php

    $filename = 'test.txt';
    
    $filestring = file_get_contents($filename);
    if ($filestring) {                         
        print $filestring                      
    } else {                                   
        print "Could not open $filename.\n";   
    }                                          

file()
======

.. code-block:: php

    $filename = "test.txt";
    
    $filearray = file($filename);
    
    if ($filearray) {
       while (list($var, $val) = each($filearray)) {
           ++$var;
           $val = trim($val);
           print "Line $var: $val\n";
       }
    } else {
       print "Could not open $filename.\n";
    }

fopen() and fread()
===================

**fopen()**
  - file to open, how you would like it opened.
  - read from 'r', write to 'w', or append to 'a'.

**fread()**
  - a file handle to read, and the number of bytes to read.

fopen() : Example
=================

.. code-block:: php

    $fh_flowers = fopen("kinds_of_flowers.txt", "r")
          or die("Can't open flowers file!\n");     
                                                    
    $fh_logfile = fopen("application.log", "w")    
        or die("Log file not writeable!\n");        

fread() : Example 1
===================

.. code-block:: php

    $huge_file = fopen("VERY_BIG_FILE", "r");
                                             
    while (!feof($huge_file)) {              
        print fread($huge_file, 1024);       
    }                                        
                                             
    fclose($huge_file);                      

fread() : Example 2
===================

.. code-block:: php

    $zipfile = fopen("data.zip", "r");
    
    if (fread($zipfile, 2) != "PK") {
        print "data.zip is not a valid Zip file!";
    }
    
    fclose($zipfile);

Reading by line using fgets()
=============================

.. code-block:: php

    $access_log = fopen("access_log", "r");
                                           
    while (!feof($access_log)) {           
        $line = fgets($access_log);        
                                           
        if (preg_match("/^Error:/", $line)) {
            print $line;                     
        }                                    
    }                                        
                                             
    fclose($access_log);                     

Lab : Reading Files
===================

Use 5 of the following:

  - readfile()
  - file_get_contents()
  - file()
  - fopen()
  - fread()
  - fgets()

===========================
Creating and Changing Files
===========================

file_put_contents()
===================

.. code-block:: php

    $filename = 'new_file.txt';
    
    $myarray[] = "This is line one";
    $myarray[] = "This is line two";
    $myarray[] = "This is line three";

    $mystring = implode("\n", $myarray);
    $numbytes = file_put_contents($filename, "$mystring\n");
    print "$numbytes bytes written";

fwrite()
========

.. code-block:: php

    $handle = fopen($filename, "wb");
    $numbytes = fwrite($handle, $mystring);
    fclose($handle);                       
    print "$numbytes bytes written\n";     

rename()
========

.. code-block:: php

    $filename2 = $filename . '.old';
    $result = rename($filename, $filename2);
    if ($result) {                          
        print "$filename has been renamed to $filename2.\n";
    } else {                                                
        print "Error: couldn't rename $filename to $filename2!\n";
    }                                                             

copy()
======

.. code-block:: php

    $filename2 = $filename . '.old';
    $result = copy($filename, $filename2);
    if ($result) {                        
        print "$filename has been copied to $filename2.\n";
    } else {                                               
        print "Error: couldn't copy $filename to $filename2!\n";
    }                                                           

unlink()
========

.. code-block:: php

    if (unlink($filename)) {
        print "Deleted $filename!\n";
    } else {                         
        print "Delete of $filename failed!\n";
    }                                         

file_exists()
=============

.. code-block:: php

    if (file_exists("snapshot1.png")) {
        print "Snapshot1.png exists!\n";
    } else {                            
        print "Snapshot1.png does not exist!\n";
    }                                           

Lab : Writing Files
===================

Use 5 of the following:

  - file_put_contents()
  - fwrite()
  - rename()
  - copy()
  - unlink()
  - file_exists()

File Time Information
=====================

.. code-block:: php

    $contacts = "contacts.txt";
    $atime = fileatime($contacts);
    $mtime = filemtime($contacts);
    
    $atime_str = date("F jS Y H:i:s", $atime);
    $mtime_str = date("F jS Y H:i:s", $mtime);
    // eg June 8th 2005 16:04:15 
    
    print "File last accessed: $atime_str\n";
    print "File last modified: $mtime_str\n";


Filename Information
====================

.. code-block:: php

    $fileinfo = pathinfo($filename);
    var_dump($fileinfo);

Locking Files with flock()
==========================

.. code-block:: php

    $handle = fopen( $filename, "w");
    // open it for WRITING ("w")
    
    if (flock($handle, LOCK_EX)) {
        // do your file writes here
        flock($handle, LOCK_UN);
        // unlock the file
    } else {
        // flock() returned false, no lock obtained
        print "Could not lock $filename!\n";
    }                                          

UNIX File Permissions
=====================

.. code-block:: bash

    -rw-r--r-- 1 cedwards cedwards 4.5K 2012-11-11 09:06 start.txt

    r = 4 (read)
    w = 2 (write)
    x = 1 (execute)

Reading File Permissions and Status
===================================

  - is_readable()
  - is_writeable()
  - is_executable()
  - is_file()
  - is_dir()

Changing File Permissions and Ownership
=======================================

.. code-block:: php

    chmod("/var/www/myfile.txt", 0777);
    chmod("/var/www/myfile.txt", 0755);

Lab : File Permissions
======================

Use 7 of the following:

  - fileatime()
  - filemtime()
  - pathinfo()
  - flock()
  - is_readable(), is_writeable()
  - is_file(), is_dir()
  - chmod()

=====================
Handling File Uploads
=====================

Upload HTML Form
================

.. code-block:: html

    <form enctype="multipart/form-data" action="uploader.php" method="post">
      <input type="hidden" name="MAX_FILE_SIZE" value="200000" />
      Choose a file to upload: <input name="uploadfile" type="file" /><br />
      <input type="submit" value="Upload" />
    </form>

Upload PHP Controller
=====================

.. code-block:: php

    <?php
      $target_path = "uploads/";
      
      $target_path = $target_path . basename($_FILES['uploadfile']['name']); 
      
      if (move_uploaded_file($_FILES['uploadfile']['tmp_name'], $target_path)) {
        echo "The file " .  basename( $_FILES['uploadedfile']['name']) 
        . " has been uploaded";
      } else {
          echo "There was an error uploading the file, please try again!";
      }
    ?>

Lab : Upload
============

  - create a simple upload form
  - move uploaded file using move_uploaded_file()
  - upload image, save it and display it again (bonus)

===================
Regular Expressions
===================

Objectives : Regular Expressions
================================

  - When to use regular expressions
  - Regular expression patterns
  - Practice, practice, practice

Before
======

.. code-block:: php

    \b[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}\b


Defining Your Requirements (Discuss)
====================================

  - Define: Utah phone number
  - Define: Utah address
  - Define: secure password requirements

Basic Regexps with preg_match()
===============================

  - the pattern to match and the string to match it against
  - returns as soon as it finds the first match

preg_match() : Example
======================

.. code-block:: php

    if (preg_match("/php/i", "PHP")) {
        print "Got match!\n";
    }

Regexp Character Classes
========================

  - [abc]
  - [A-Za-z]
  - [0-9]
  - [^123]

Character Classes : Example 1
=============================

.. code-block:: php

    if (preg_match("/[Ff]oo/", "Foo"))

Character Classes : Example 2
=============================

.. code-block:: php

    if (preg_match("/[^a-z]esting/i", "Testing"))

Character Classes : Example 3
=============================

.. code-block:: php

    if (preg_match("/[A-S]esting/", "Testing"))

Lab : Regex Character Classes
=============================

Write three regular expression patterns using character classes for:

  - a name, limited to the first half of the alphabet
  - a job title in the medical industry
  - a street address

Regexp Special Characters
=========================

  - ``+``, ``*``, ``?`` 
  - { }
  - ( )
  - $, and ^

Special Characters : Example 1
==============================

.. code-block:: php

    if (preg_match("/[A-Z][A-Z0-9]+/i", "A123"))

Special Characters : Example 2
==============================

.. code-block:: php

    if (preg_match("/[0-9]?[A-Z]+/", "10GreenBottles"))

Special Characters : Example 3
==============================

.. code-block:: php

    if (preg_match("/[A-Z]?[A-Z]?[A-Z]*/", ""))

Special Characters : Example 4
==============================

.. code-block:: php

    if (preg_match("/[A-Z]{3}/", "FuZ"))

Special Characters : Example 5
==============================

.. code-block:: php

    if (preg_match("/[A-Z]{3}/i", "FuZ"))

Special Characters : Example 6
==============================

.. code-block:: php

    if (preg_match("/[A-Z]{1,5}[0-9]{2}/i", "adams42"))

Special Characters : Example 7
==============================

.. code-block:: php

    if (print preg_match("/(Linux|Mac OS X)/", "Linux"))

Special Characters : Example 8
==============================

.. code-block:: php

    if (print preg_match("/contra(diction|vention)/", "contravention"))

Special Characters : Example 9
==============================

.. code-block:: php

    if (print preg_match(
    "/Windows (([0-9][0-9]+|Me|XP)|Codename (Whistler|Longhorn))/", 
    "Windows Codename Whistler"))

Special Characters : Example 10
===============================

.. code-block:: php

    if (preg_match("/\?$/", "Does this match?"))

Special Characters : Example 11
===============================

.. code-block:: php

    if (preg_match("/\?$/", "Does this match? Are you sure."))

Special Characters : Example 12
===============================

.. code-block:: php

    if (preg_match("/^PHP FTW!/", "PHP FTW!!!!!"))

Lab : Regex Special Characters
==============================

Use each of the following in any number of preg_match() tests:

  - ``+``, ``*``, ``?``
  - { }
  - ( )
  - $, and ^

Words and Whitespace Regexps
============================

  - .
  - \s, \S
  - \b, \B

Words and Whitespace : Example 1
================================

.. code-block:: php

    if (preg_match("/c.t/", "cart"))

Words and Whitespace : Example 2
================================

.. code-block:: php

    if (preg_match("/[\S]{7}[\s]{1}[\S]{6}/", "Foolish Child"))

Words and Whitespace : Example 3
================================

.. code-block:: php

    if (preg_match("/no\b/", "he said 'no!'"))

Storing Matched Strings
=======================

.. code-block:: php

    $a = "Foo moo boo tool foo!";
    preg_match_all("/[A-Za-z]oo\b/i", $a, $matches);

After
=====

.. code-block:: php

    \b[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}\b

Lab : Regular Expressions
=========================

  - Define a regular expression to match:
     - valid email addresses
     - valid IP addresses


===============================
Attributes of a Good Programmer
===============================

Laziness
========

**Laziness** - The quality that makes you go to great effort to reduce overall
energy expenditure. It makes you write labor-saving programs that other people
will find useful, and document what you wrote so you don't have to answer so
many questions about it. Hence, the first great virtue of a programmer. Also
hence, this book. See also impatience and hubris.

Impatience
==========

**Impatience** - The anger you feel when the computer is being lazy. This makes
you write programs that don't just react to your needs, but actually anticipate
them. Or at least pretend to. Hence, the second great virtue of a programmer.
See also laziness and hubris.

Hubris
======

**Hubris** - Excessive pride, the sort of thing Zeus zaps you for. Also the
quality that makes you write (and maintain) programs that other people won't
want to say bad things about. Hence, the third great virtue of a programmer.
See also laziness and impatience.

=============
Final Project
=============

Create a contest submission page and PHP backend.

Project Requirements
====================

  - Full Name
  - Email (validated)
  - Country (drop-down)
  - File Upload
  - Terms Agreement Checked
  - Spam Deterrent Mechanism
  - Separate Error and Success Pages
  - Email all information, including attachment, and CC: submitter

Class Review
============

Please take 5 minutes to review your class experience.

Additional Study
================

Code Academy PHP Track

 - http://www.codecademy.com/tracks/php

Conclusion
==========

**Questions? / Comments?**

christer.edwards@gmail.com
