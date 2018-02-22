# utl_find_the_parameters_of_probability_density_function_that_yeild_a_given_probability

    Find the parameters of probability density function that yeild a given probability

    github
    https://goo.gl/Ca1cvh
    https://github.com/rogerjdeangelis/utl_find_the_parameters_of_probability_density_function_that_yeild_a_given_probability

    WPS/Proc Python

    see
    https://stackoverflow.com/questions/46077610/how-to-store-a-print-output-to-variable

    Unlike relying on just R, SAS/WPS has good integration with Python, Perl and R.
    SAS/IML and WPS/IML have good numerical integration and 'solve' functions.

    Just two lines of code in Python

    INPUT
    =====

    Problem solve for a given

              0.5
             /
       .5 =  |  a*x**(a-1)*exp(-(x**a)) dx
            /
            0

     This seems like the closest pdf to your problem, area=1 unnder this pdf.


     PROCESS (PYTHON WORKING CODE - note I manually put the symy integration into solve
              This is due to my lack of skill with sympy)
     ==================================================================================

       ntrgrl=integrate(a*x**(a-1)*exp(-(x**a)),x);
       solv=solve(-exp(-(.5**a)) + 1 - .5);

    OUTPUT
    ======

       a*Piecewise((exp(-1)*log(x), Eq(a, 0)), (-exp(-x**a)/a, True))

       This is the value of a that yeilds 50% probability
       a = [0.528766372944898]

    SOLUTION and SAS/WPS code
    =========================

    %utl_submit_wps64("
    options set=PYTHONHOME 'C:\Users\backup\AppData\Local\Programs\Python\Python35\';
    options set=PYTHONPATH 'C:\Users\backup\AppData\Local\Programs\Python\Python35\lib\';
    proc python;
    submit;
    from sympy import *;
    import pandas as pd;
    x, r, a = symbols('x r a');
    ntrgrl=integrate(a*x**(a-1)*exp(-(x**a)),x);
    solv=solve(-exp(-(.5**a)) + 1 - .5);
    print(ntrgrl);
    print(solv);
    outputs = [];
    outputs.append(solv);
    print(outputs);
    df=pd.DataFrame(outputs);
    df.to_clipboard(index=False,header=False);
    endsubmit;
    run;quit;
    ",returnVarName=fromPy);

    * SAS checking the solution;
    data check;

      a=&frompy;

      * the cumulative distribution for the Weibul distribution is;
      * F(x) = 1 - exp(-(x**a)) as sympy derived;

      /* lets see if the sympy result a = [0.528766372944898]

              0.5
             /
       .5 =  |  a*x**(a-1)*exp(-(x**a)) dx
            /
            0
      */

      fx = 1 - exp(-(.5**a));

      put fx=;

    run;quit;

    Log

    FX=0.5

     Ricks Response
     
     This is essentially one-dimensional root-finding problem. For two or more parameters,
    it becomes optimization of a function of an integral. For example:
    Given the Beta(a,b) PDF, what values of the parameters maximize the probability that X is in[0.6, 0.8]?

    Answer and SAS code at
    https://blogs.sas.com/content/iml/2014/03/05/optimizing-a-function-that-evaluates-an-integral.html



