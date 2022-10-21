
## Git-keeper Student Workflow

The workflow for most git-keeper assignments is:

1. Receive an email with a clone URL for a Git repository on the server
2. Clone the repository using the URL from the email
3. Do your work in the local clone of the repository
4. Add and commit your changes to the local repository
5. Push your changes back to the server
6. Check your email to make sure the submission was received and see the results of any tests that were run. If the tests did not pass, you can go back to step 3 and submit again.


## Example: Homework Average

* If you have not done so already, fill out the form at [www.yellkey.com/pattern](http://www.yellkey.com/pattern) to get added to the sample course.
* You will receive an email from `gitkeeper@moravian.edu` containing a clone URL and directions for your first assignment.


  ```
  Clone URL: <username>@ec2-18-205-161-251.compute-1.amazonaws.com:/home/<username>/colemanb/ccsce2022/hw_average.git


  This is the first programming problem for CCSC-E 2022.  
  Your task is to implement the function compute_hw_average 
  that takes a list of homework scores as its parameter.

  The following built-in Python functions will help:

    * len(lst) - returns the length of a list
    * min(lst) - returns the minimum value in a list
    * sum(lst) - returns the sum of the elements in a list
  ```

* You should clone the repo onto your laptop (using the username and password sent to you in an email from `gitkeeper@moravian.edu`).  You can use the command line or the git features of an IDE.

  ```
  git clone <username>@ec2-18-205-161-251.compute-1.amazonaws.com:/home/<username>/colemanb/ccsce2022/hw_average.git
  ```
  
* Edit the code with an **incorrect** solution

  ```
  def compute_hw_average(grades):
      """
      This function computes the average homework grade for a student.
      grades is a list of integer homework scores, and the average is
      computed by dropping the lowest grade.  If the list only contains
      a single value, no grades are dropped.
      """
      return sum(grades) / len(grades)
  ```
      
* Commit the changes and push them back to the server

  ```
  git add hw_average.py
  git commit -m "my first attempt"
  git push origin master
  ```
  
* Check for email from `gitkeeper@moravian.edu` that contains feedback about your solution

	```
	Below is the output from running my tests against your code.
	============================= test session starts ==============================
	platform linux -- Python 3.10.4, pytest-7.1.3, pluggy-1.0.0
	rootdir: /home/tester/tests
	collected 5 items
	
	test_compute_hw_average.py F..FF                                         [100%]
	
	=================================== FAILURES ===================================
	_____________________ test_no_submissions_is_zero_average ______________________
	
	    def test_no_submissions_is_zero_average():
	>       assert compute_hw_average([]) == approx(0)
	
	test_compute_hw_average.py:6:
	_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _
	
	grades = []
	
	    def compute_hw_average(grades):
	        """
	        This function computes the average homework grade for a student.
	        grades is a list of integer homework scores, and the average is
	        computed by dropping the lowest grade.  If the list only contains
	        a single value, no grades are dropped.
	        """
	>       return sum(grades) / len(grades)
	E       ZeroDivisionError: division by zero
	
	hw_average.py:10: ZeroDivisionError
	__________________________ test_lowest_grade_dropped ___________________________
	
	    def test_lowest_grade_dropped():
	>       assert compute_hw_average([0, 5, 7]) == approx(6)
	E       assert 4.0 == 6 ± 6.0e-06
	E         comparison failed
	E         Obtained: 4.0
	E         Expected: 6 ± 6.0e-06
	
	test_compute_hw_average.py:18: AssertionError
	____________________________ test_average_is_float _____________________________
	
	    def test_average_is_float():
	>       assert compute_hw_average([0, 1, 2, 3, 4]) == approx(10/4)
	E       assert 2.0 == 2.5 ± 2.5e-06
	E         comparison failed
	E         Obtained: 2.0
	E         Expected: 2.5 ± 2.5e-06
	
	test_compute_hw_average.py:22: AssertionError
	=========================== short test summary info ============================
	FAILED test_compute_hw_average.py::test_no_submissions_is_zero_average - Zero...
	FAILED test_compute_hw_average.py::test_lowest_grade_dropped - assert 4.0 == ...
	FAILED test_compute_hw_average.py::test_average_is_float - assert 2.0 == 2.5 ...
	========================= 3 failed, 2 passed in 0.03s ========================== 
	```
	
* Edit your code to have the **correct** solution

	```
	def compute_hw_average(grades):
	    """
	    This function computes the average homework grade for a student.
	    grades is a list of integer homework scores, and the average is
	    computed by dropping the lowest grade.  If the list only contains
	    a single value, no grades are dropped.
	    """
	    if len(grades) == 0:
	        return 0
	    if len(grades) == 1:
	        return grades[0]
	    return (sum(grades) - min(grades)) / (len(grades) - 1)
	```
	
* Commit the changes and push them back to the server

  ```
  git add hw_average.py
  git commit -m "got it this time"
  git push origin master
  ```
  
* Check for email from `gitkeeper@moravian.edu` that contains feedback about your solution

	```
	Below is the output from running my tests against your code.
	============================= test session starts ==============================
	platform linux -- Python 3.10.4, pytest-7.1.3, pluggy-1.0.0
	rootdir: /home/tester/tests
	collected 5 items
	
	test_compute_hw_average.py .....                                         [100%]
	
	============================== 5 passed in 0.01s ===============================
	```