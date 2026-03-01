+++
title = "April Fedora Meetup - Unit Testing Revealed !!!"
slug = "april-fedora-meetup-unit-testing-revealed"
description = "A practical post about april fedora meetup - unit testing revealed !!! with lessons, examples, and useful takeaways."
date = "2016-04-24"
category = "Meetup"
author = "Suraj Narwade"
+++

April Fedora meetup was dedicated to “Unit testing”.

As I am into DevOps, usually i had seen various automated tests  that was carried out by jenkins are nothing but unit test but i  was always wondered what is this??
Kushal gave us brief idea about unit test :

*unit testing is a method by which individual units of source code, sets of one or more computer program modules together are tested to determine if they are fit for use.*

as well unit test gives you opportunity to scrutinize your own code for proper operation.

And also he gave an example of unit test for ‘cat’ command, for that we cloned https://github.com/kushaldas/tunirtests ( as we had to used some functions from these files )
```
 import unittest
 from testutils import system
 class Firetest(unittest.TestCase): 
    def test_cat(self):
    	out,err,retcode= system("cat /etc/fedora-release")
    	out = out.decode('utf-8')
    	self.assertIn('Fedora',out)
 if __name__ == '__main__' :
     unittest.main()
```
Then, Kushal and Pravin gave different test cases to each one of us, I got test case to check selinux context of file ( i.e. restorecon command ) whether we are getting same result after 'restorecon' or not:
```
 import unittest
 from testutils import system
 class Firetest(unittest.TestCase) : 
    def test_selinux(self):
        out,err,retcode= system("ls -lZ /var/www/html/index.html")
        out = out.decode('utf-8')
        out1,err1,retcode1= system("restorecon -v /var/www/html/index.html")
        out2,err2,retcode2= system("ls -lZ /var/www/html/index.html")
        out2 = out2.decode('utf-8')
        self.assertEqual(out,out2)
 if __name__ == '__main__' : 
    unittest.main()
```
As I was new to fedora meetup, i got connected with new friends too.

This experiment of meetup was awesome as it was kind of interactive,  rather than just one person talking and others are just listening.

<img src="/fedora.png" width="600" height="400" />
<img src="/fedora1.png" width="600" height="400" />

1. forget to mention above, we had tasty snacks and drinks too !!! :P
   

Thank you, [ChandanKumar](https://twitter.com/ciypro), [Kushal Das](https://twitter.com/kushaldas), [PravinKumar](https://twitter.com/kumar_pravin) and all !!!
for more regarding unit tests, read here : http://pymbook.readthedocs.org/en/latest/testing.html#unit-testing
