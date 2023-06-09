Download Link: https://assignmentchef.com/product/solved-se465-project
<br>
<h1>Part (I) Building an Automated Bug Detection Tool</h1>

You will learn <em>likely-invariants </em>from call graphs in a way reminiscent to that in SOSP’01 paper discussed in class (Coverity). In particular, you will learn what pairs of functions are called together. You will then use these likely-invariants to automatically detect software bugs.

<h2>(a) Inferring Likely Invariants for Bug Detection</h2>

Take a look at the following contrived code segment (also seen in lecture):

void scope1() {

A(); B(); C(); D();

} void scope2() {

A(); C(); D();

} void scope3() {

A(); B(); B();

} void scope4() {

B(); D(); scope1();

} void scope5() {

B(); D(); A();

} void scope6() { B(); D();

}

We can learn that function A and function B are called together three times in function scope1, scope3, and scope5. Function A is called four times in function scope1, scope2, scope3, and scope5. We infer that the one time that function A is called without B in scope2 is a bug, as function A and B are called together 3 times. We only count B once in scope3 although scope3 calls B two times.

We define <em>support </em>as the number of times a pair of functions appears together. Therefore, <em>support</em>({<em>A,B</em>}) is 3. We define <em>confidence</em>({<em>A,B</em>}<em>,</em>{<em>A</em>}) as <em><sup>support</sup></em><em><sub>support</sub></em><sup>({</sup><sub>(</sub><em><sup>A,B</sup></em><sub>{<em>A</em>})</sub><sup>})</sup>, which is . We set the threshold for support and confidence to be <em>T SUPPORT </em>and <em>T CONFIDENCE</em>, whose default values are 3 and 65%. You only print bugs with confidence <em>T CONFIDENCE </em>or more and with pair support <em>T SUPPORT </em>times or more. For example, function B is called five times in function scope1, scope3, scope4, scope5, and scope6. The two times that function B is called alone are not printed as a bug as the confidence is only 60%  , lower than the <em>T THRESHOLD</em>, which is 65%. Please note that <em>support</em>(<em>A,B</em>) and <em>support</em>(<em>B,A</em>) are equal, and both 3.

Perform intra-procedural analysis. For example, do not expand scope1 in scope4 to the four functions A, B, C, and D. Match function names only.

The sample output with the default support and confidence thresholds should be:

bug: A in scope2, pair: (A, B), support: 3, confidence: 75.00% bug: A in scope3, pair: (A, D), support: 3, confidence: 75.00% bug: B in scope3, pair: (B, D), support: 4, confidence: 80.00% bug: D in scope2, pair: (B, D), support: 4, confidence: 80.00%

<strong>Generate call graphs using LLVM. </strong>Given a bitcode file, use LLVM <em>opt </em>to generate call graphs in plain text format, and then analyze the textual call graphs to generate function pairs and detect bugs. The <em>opt </em>tool is installed on the ECE machines (ecetesla0.uwaterloo.ca) as well as the CS machines. If you want to work on your own computer, you need to use 64-bit LLVM 3.0 to avoid compatibility issues. LLVM 3.0 is available as a package in many popular Linux distributions.

A sample call graph in text format is shown as follows:

Call graph node for function: ‘ap_get_mime_headers_core’&lt;&lt;0x42ff770&gt;&gt; #uses=4

CS&lt;0x4574458&gt; calls function ‘ap_rgetline_core’

CS&lt;0x4575958&gt; calls external node

CS&lt;0x4575a50&gt; calls external node

CS&lt;0x4575b20&gt; calls function ‘apr_table_setn’

CS&lt;0x45775a8&gt; calls external node

CS&lt;0x45776a0&gt; calls external node

CS&lt;0x4577770&gt; calls function ‘apr_table_setn’ CS&lt;0x45783b8&gt; calls external node

CS&lt;0x4579dc0&gt; calls function ‘apr_table_setn’

CS&lt;0x4579f78&gt; calls function ‘strchr’

CS&lt;0x457a948&gt; calls external node

CS&lt;0x457aa40&gt; calls external node

CS&lt;0x457ab10&gt; calls function ‘apr_table_setn’

CS&lt;0x457d9d0&gt; calls function ‘apr_table_addn’

CS&lt;0x457e4e8&gt; calls function ‘apr_table_compress’ and

Call graph node &lt;&lt;null function&gt;&gt;&lt;&lt;0x2411e40&gt;&gt; #uses=0

The following explanation should help you understand the call graph:

<ul>

 <li>The above call graph represents the function ap get mine headers core calls functions ap rgetline core, apr table setn, etc.</li>

 <li><strong>#uses= </strong>gives the number of times the caller is called by other functions. For example, ap get mine headers core is called 4 times.</li>

 <li><strong>CS </strong>denotes call site.</li>

 <li><strong>0x????? </strong>indicates the memory address of the data structure, i.e., call graph nodes and call sites.</li>

 <li>An <strong>external node </strong>denotes a function that is not implemented in this object file. Ignore external node functions.</li>

 <li>A call graph node of <strong>null function </strong>represents all external callers. Ignore null function nodes entirely.</li>

</ul>

<strong>Performance. </strong>We will evaluate the performance and scalability of your program on a pass/fail basis. The largest program that we test will contain up to 20k nodes and 60k edges. Each test case will be given a timeout of two minutes. A timed-out test case will receive zero points. We do not recommand using multi-threading because it only provides a linear gain in performance.

Hints:

<ul>

 <li>(Very Important!) Do not use string comparisons because it will greatly impact the performance. Use a hashing method to store and retrieve your mappings.</li>

 <li>Minimize the number of nested loops. Pruning an iteration in the outer loop is more helpful than in the inner loop.</li>

 <li>None of the provided test cases should take more than 5 seconds to run.</li>

</ul>

<strong>Submission protocol.         </strong>Please follow this protocol and read it <em>carefully</em>:

<ul>

 <li>You only have to submit three items inside the a directory named as pi/partA. Your .gitignore should ensure that you don’t push compiled binaries or test

  <ul>

   <li>A file called Makefile to compile your source code; it must include ‘all’ and ‘clean’ targets.</li>

   <li>A file called pipair as an executable script. You don’t need to submit this if your pipair is an executable binary which will be generated automatically when our script executes ‘make’ on your Makefile.</li>

   <li>Source code for your program. Please document/comment your code properly so that it is understandable. Third party libraries are allowed. Make sure all your code runs on ecelinux. We will not edit any environment variables when we run your code on our account, so make sure your program is self-contained. We will be testing the code under the bash shell.</li>

  </ul></li>

</ul>

Place all of the above items at the root of your repository. Do not submit any of the files from the skeleton folder.

<ul>

 <li>We provide the marking script (<em>sh</em>) that we’ll use to test your pipair. Please write your code and Makefile to fit the script.</li>

 <li><strong>Make sure your code works on ecelinux machines</strong>. We will use the provided scripts to grade your project on ecelinux machines. If your code doesn’t run on ecelinux, you will receive 0 points. <strong>You must use C/C++ or Java for part (I).</strong></li>

 <li>Marking will be done with strict line comparisons. We will count how many pairs are missing (false negatives) and how many pairs are false positives.</li>

 <li>You should print one pair per line to stdout in this format:</li>

</ul>

bug: %s in %s, pair: (%s, %s), support: %d, confidence: %.2f%%


<ul>

 <li>The order of the pairs in your output does not matter. As you can see from the script <em>sh</em>, we will run <em>sort </em>before <em>diff </em>.</li>

 <li>The order of the two functions in a pair does not matter in the calculations. Both (foo, bar) and (bar, foo) contribute to the count of pair (bar, foo). However, to have consistent output (for easier grading), you must sort the pair alphabetically while printing. For example, print (bar, foo) instead of (foo, bar).</li>

</ul>

The golden output uses natural sorting (lexicographical comparison) with the following API:

<a href="https://docs.oracle.com/javase/tutorial/collections/interfaces/order.html">http://docs.oracle.com/javase/tutorial/collections/interfaces/order.html</a>

In lexicographical comparison, digits will precede letters and uppercase precedes lowercase.

<ul>

 <li>Ignore duplicate calls to the same function. For example, if we have the code segment scope() { A(); B(); A(); B(); A(); }, the support for the pair (A, B), <em>support</em>(<em>A,B</em>), is 1.</li>

 <li>Name your program pipair. We must be able to run it with the following command, where the support and confidence value will always be an integer:</li>

</ul>

<em>./pipair &lt;bitcode file&gt; &lt;T SUPPORT&gt; &lt;T CONFIDENCE&gt;</em>, e.g.

<em>./pipair hello.bc 10 80</em>, to specify support of 10 and confidence of 80%,

or

<em>./pipair &lt;bitcode file&gt;</em>, e.g.

<em>./pipair hello.bc</em>, to use the default support of 3 and default confidence of 65%.

<ul>

 <li>If running your program requires a special command line format such as <em>java YourProgram arguments</em>, your <em>pipair </em>should be a wrapper bash script, e.g.:</li>

</ul>

#!/bin/bash java YourProgram <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="ebcfab">[email protected]</a>

<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="446004">[email protected]</a> represents all command line arguments.

<ul>

 <li>If you are using Java, you must configure the -Xms128m and -Xmx128m flag to ensure Java Virtual Machine allocates enough memory during the startup (do not allocate more than 128MB). It is recommended that configure this in your pipair script while launching your Java program. The reason is that the VM will not dynamically increase the heap size for the larger test cases. Make sure your program is memory-efficient to ensure it succeeds on larger test cases.</li>

</ul>

<strong>Skeleton files and marking.</strong>

<ul>

 <li>We recommend developing on ecelinux. We have confirmed that the UNIX “sort” command behaves differently on Mac than on Linux. (You may be able to find command-line arguments to fix this, but it’s at your own risk.)</li>

 <li>To test a specific test case, run “make clean” and then “make” in the test’s directory. Your output should be identical to the “gold” file. Your output should be passed through sort before diffing with the gold For example:</li>

</ul>

sort testX Y Z.out | diff gold Y Z –

<ul>

 <li>To run all tests together, execute sh. Logs of all output can be found in /tmp.</li>

 <li>sh runs “make clean” in all test directories.</li>

 <li>Our marking procedure:

  <ol>

   <li>We will extract your submitted tar file on the server.</li>

   <li>We will copy the required files from your pi/partA directory into the root of a clean skeleton folder that contains the full test suite with seven tests.</li>

   <li>We will run “sh” to test your program, which will automatically:

    <ul>

     <li>Run “make” to compile your program.</li>

     <li>Execute each test with a two minute limit.</li>

    </ul></li>

  </ol></li>

</ul>

<ul>

 <li>Since the skeleton files and tests are copied over during marking, do <em>not </em>modify any files given in the project skeleton, since they will be over-written.</li>

</ul>

<strong>Common issues.</strong>

<ul>

 <li>It says “error”.</li>

</ul>

This error indicates verify.sh encountered a problem when it tried to run make inside the test directories. This is likely due to a failure in pipair.

Here are a few things that you can do to help troubleshoot this issue:

<ul>

 <li>The error message is usually in the testx x xx.out This file contains what got written to stdout by your pipair executable.</li>

 <li>The log output of sh is dumped to a file at /tmp/testing-<em>&lt;</em>your username<em>&gt;</em>-pi-<em>&lt;</em>time of log<em>&gt;</em>.log.</li>

 <li>Instead of testing the whole test suite using sh, run the individual test cases manually with “make clean” and then “make” inside each testX folder.</li>

 <li>Verify the correctness of your pipair by running it manually inside each test folder. If your pipair is a bash script that consists of multiple commands, manually execute the bash commands one at a time.</li>

</ul>

<ul>

 <li>How should I implement pipair?</li>

</ul>

You can implement pipair as an executable binary, or you can implement pipair as a bash script. It might be easier if you do it the bash script approach. The reason is that you can call opt within the bash script and either

<ul>

 <li>pipe the call graph output from opt into your C/Java program; or,</li>

 <li>write the call graph to a file and then read the file with your C/Java program to perform the analysis.</li>

</ul>

<ul>

 <li>My program is printing garbage; how can I get rid of message X or message Y?</li>

</ul>

Please check both stdout and stderr. You can discard those messages by dumping them to /dev/null.

<ul>

 <li>I don’t know how to get the call graph using opt. I’m getting either nothing or gibberish.</li>

</ul>

Use LLVM’s opt tool. opt prints the call graph to stderr, and the contents of the bitcode file to stdout if stdout is not the terminal. There are many ways of getting the call graphs:

<ul>

 <li>Use an intermediate file for the call graph.</li>

 <li>Call opt from inside your program, and capture opt’s stderr.</li>

 <li>Write a wrapper, and pipe the call graph to your program as if it is typed by hand to stdin. For example: opt -print-callgraph 2<em>&gt;</em>&amp;1 <em>&gt;</em>/dev/null | YourProgram <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="351175">[email protected]</a></li>

</ul>

There are many other ways depending on your design and your language of choice.

<ul>

 <li>I can’t run a shell command with I/O redirection (e.g., “opt -print-callgraph 2<em>&gt;</em>&amp;1 <em>&gt;</em>/dev/null”).</li>

</ul>

This is because your Linux shell does not support I/O redirection. You can check what shell you are running by executing “ps -p $$”. Change your shell to bash by issuing “bash” in the terminal to fix the issue.

<ul>

 <li>I’m getting “Segmentation Fault” from opt.</li>

</ul>

If it’s only test3, you are using an old version of LLVM. You must use 64-bit LLVM 3.0 to generate a call graph for test3. If all tests segfault, you are using a broken LLVM compilation. We recommend using LLVM on ecelinux.

<ul>

 <li>Java throws “NoClassDefFoundError” if I run my program within the testX directory.</li>

</ul>

The working directory while running the tests is always <em>inside </em>the testX directories. Therefore, you need to specify the path to your .class file using the “-cp” option in your pipair wrapper. For example: java -cp [classpath here] YourClassName <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="b99df9">[email protected]</a>”

<h2>(b) Finding and Explaining False Positives (10 points)</h2>

Examine the output of your code for (a) on the Apache httpd server (test3 from part (a)) by running verify.sh. Do you think you found any real bugs? There are some false positives. A false positive is where a line says “bug …” in your output, but there is nothing wrong with the corresponding code. Write down two different fundamental reasons for as to why false positives occur in general.

Identify 2 pairs of functions that are false positives for httpd. Explain why you think each of the two pairs that you selected are false positives. The two pairs should have a combined total of at least 10 “bug …” line locations (e.g., first pair can have 4 and second pair can have 6). For example, the following sample output contains 4 locations regarding 3 pairs: (A, B) (A, D) and (B, D). You do not have to provide explanations for all 10 locations, but you will want to use at least one location to discuss each pair. Briefly explain why the pair is a false positive with reference to the source code.

bug: A in scope2, pair: (A, B), support: 3, confidence: 75.00% bug: A in scope3, pair: (A, D), support: 3, confidence: 75.00% bug: B in scope3, pair: (B, D), support: 4, confidence: 80.00% bug: D in scope2, pair: (B, D), support: 4, confidence: 80.00%

The source code of httpd has been provided on ecelinux for your convenience. It is located at: <em>/opt/testing/apache src</em>.

This directory contains the source code, the bitcode file (.bc), the call graph file in plain text format (.txt), and the sample output (.out) for a run of httpd. At the end of the call graph, you’ll find the file path where each function is defined, which will help you find the actual function bodies.

If you find new bugs (bugs that have not been found by other people yet), you may receive bonus points. Check the corresponding bug databases of Apache<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a> to make sure that the bug you found has not been reported there already. Clearly explain why you think it is a bug and provide evidence that it is a new bug.

<h2>(c) Inter-Procedural Analysis. (10 points)</h2>

One solution to reduce false positives is inter-procedural analysis. For example, one might expand the function scope1 in the function scope4 to the four functions A, B, C, and D. Implement this solution by adding an optional fourth command line parameter to pipair. We leave the algorithm’s implementation details up to you.

Write a report (up to 2 pages) to describe your algorithm and what you found. Run an experiment on your algorithm and report the details. For example, you can vary the numbers of levels that you expand as an experiment. Provide a concrete example to illustrate that your solution can do better than the default algorithm used in (a). We will read your report and code. If you only submit a report, you will receive at most 50% of the points for this part (c). Make sure your code is well documented and your report is understandable. Follow the submission protocol in part (a) and place the code inside the <strong>pi/partC </strong>directory.

Test 3 from part (a) utilizes the same program (httpd) as part (b). Therefore, you might find it convenient to use test 3 to verify your inter-procedural experiment.

Resources for students who would like to generate a call graph with function arguments (optional):

<ul>

 <li>Check out the following post: http://stackoverflow.com/questions/5373714/generate-calling-graph-for-ccode</li>

 <li>Once you generated the call graph, use c++filt to demangle the function names (takes care of function overloading). After that use sed to transform the text and you will get the call graph.</li>

 <li>It is important that you disable C/C++ optimizations during the call graph generation. Add the following flags in “opt” to ensure there is no inlining and ensure “opt” would not optimize the function arguments: -disable-inlining -disable-opt</li>

 <li>You have to use clang++ to generate the bitcode.</li>

</ul>

<h1>Part (II) — Using a Static Bug Detection Tool</h1>

For this part of the project you will be using the static code analysis tool Coverity to find defects in source code.

We have acquired a Coverity license for student use. Occasionally, we will collect groups, generate Coverity credentials, and mail you a link which allows you choose a password for your credentials.

<h2>(a) Resolving Bugs in Apache Commons</h2>

In this task, you are to triage warning messages resulting from running Coverity static analysis on Apache Commons (a library of useful Java tools). We have already compiled and analyzed a version of Apache Commons for you. The pre-analyzed code is available on Coverity’s web interface at <a href="http://ecetesla0.uwaterloo.ca:8080/">http://ecetesla0.uwaterloo.ca: </a><a href="http://ecetesla0.uwaterloo.ca:8080/">8080</a> behind the firewall. Log in using username “student” and password “%Ccb2wXQ”. To view and triage warnings from the analysis, select the “Outstanding Defects” item in the navigation tree on the left hand side of the page. Click on warnings to view details of the warning and the source code that triggered the warning.

The analysis results contain warnings from both Coverity and FindBugs static analysis tools.

Your task is to triage and repair these warnings. There are 20 warnings for you to triage and repair. Triage each warning by classifying it as one of the following: <em>False Positive</em>, <em>Intentional </em>or <em>Bug</em>. Do not use the built-in Triage tool. Write your classification and explanations directly in your report. Below are descriptions of each triage classification:

<ul>

 <li><strong>False Positive</strong>: The warning does not indicate a fault or deficiency.</li>

 <li><strong>Intentional</strong>: You believe the warning points to code that the developer <strong>intentionally </strong>created that is incorrect (contains a fault) or considered bad practice.</li>

 <li><strong>Bug</strong>: The warning points to a defect (a fault or bad practice) in the code.</li>

</ul>

If you classify the warning as <em>False Positive</em>, provide an explanation as to why you believe this is the case. If you classify the warning as <em>Intentional</em>, explain how the code can be re-factored to remove the warning, or explain why it should be left as-is. If you classify the warning as bug, provide the faulty lines (1–2 lines is often enough, including the file name and line numbers) and a description of a proposed bug fix.

The difference between False Positive/Intentional and Intentional/Bug is not always clear, so there is not necessary one unique good classification. Make sure you explain clearly your choice!

For details on each Coverity checker, read the checker documentation (see <a href="https://patricklam.ca/stqam/coverity/#reference_material">https://patricklam.ca/stqam/ </a><a href="https://patricklam.ca/stqam/coverity/#reference_material">coverity/#reference_material</a><a href="https://patricklam.ca/stqam/coverity/#reference_material">)</a>. Information on FindBugs checkers can be found at <a href="http://findbugs.sourceforge.net/bugDescriptions.html">http://findbugs. </a><a href="http://findbugs.sourceforge.net/bugDescriptions.html">sourceforge.net/bugDescriptions.html</a><a href="http://findbugs.sourceforge.net/bugDescriptions.html">.</a>

The report for this part should be no more than 4 pages. Be concise.

<h2>(b) Analyzing Your Own Code</h2>

Run Coverity on your own code from part (I) (a). See instructions on <a href="https://patricklam.ca/stqam/coverity/">https://patricklam.ca/stqam/ </a><a href="https://patricklam.ca/stqam/coverity/">coverity/</a> to analyze your code with Coverity Connect interface. Once your project is uploaded on the web interface, you must <strong>make it inaccessible to other students: connect on </strong><a href="http://ecetesla0.uwaterloo.ca:8080/">http://ecetesla0.uwaterloo.ca: </a><a href="http://ecetesla0.uwaterloo.ca:8080/">8080</a> <strong>with your credentials. Click on “Configuration” , “Projects &amp; Streams” and Select your project. In the “Roles” tab, click on “add” and check “<u>No Access</u>” for the group “Students”.</strong>

You learn more from having the tool find defects in code that you wrote, fixing the problem and seeing the defect go away. Discuss two of the bugs detected by Coverity using up to 1 page. If Coverity finds no bugs, what are the possible reasons?

Include the results from your analysis with your submission. Put them in directory pii.

You can find instructions on how to use Coverity to build and analyze your code at <a href="https://patricklam.ca/stqam/coverity/">https://patricklam.ca/ </a><a href="https://patricklam.ca/stqam/coverity/">stqam/coverity/</a><a href="https://patricklam.ca/stqam/coverity/">.</a>

<a href="#_ftnref1" name="_ftn1">[1]</a> <a href="https://issues.apache.org/bugzilla/">https://issues.apache.org/bugzilla/</a>