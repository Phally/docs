Debugging
#########

Debugging is an inevitable and necessary part of any development
cycle. While CakePHP doesn't offer any tools that directly connect
with any IDE or editor, CakePHP does provide several tools to
assist in debugging and exposing what is running under the hood of
your application.

Basic Debugging
===============

.. php:function:: debug(mixed $var, boolean $showHtml = null, $showFrom = true)

    :param mixed $var: The contents to print out. Arrays and objects work well.
    :param boolean $showHTML: Set to true, to enable escaping. Escaping is enabled
        by default in 2.0 when serving web requests.
    :param boolean $showFrom: Show the line and file the debug() occurred on.

The debug() function is a globally available function that works
similarly to the PHP function print\_r(). The debug() function
allows you to show the contents of a variable in a number of
different ways. First, if you'd like data to be shown in an
HTML-friendly way, set the second parameter to true. The function
also prints out the line and file it is originating from by
default.

Output from this function is only shown if the core debug variable
has been set to a value greater than 0.

.. versionchanged:: 2.1 
    The output of ``debug()`` more resembles ``var_dump()``, and uses
    :php:class:`Debugger` internally.

Debugger Class
==============

The debugger class was introduced with CakePHP 1.2 and offers even
more options for obtaining debugging information. It has several
functions which are invoked statically, and provide dumping,
logging, and error handling functions.

The Debugger Class overrides PHP's default error handling,
replacing it with far more useful error reports. The Debugger's
error handling is used by default in CakePHP. As with all debugging
functions, ``Configure::debug`` must be set to a value higher than 0.

When an error is raised, Debugger both outputs information to the
page and makes an entry in the error.log file. The error report
that is generated has both a stack trace and a code excerpt from
where the error was raised. Click on the "Error" link type to
reveal the stack trace, and on the "Code" link to reveal the
error-causing lines.

Using the Debugger Class
========================

.. php:class:: Debugger

To use the debugger, first ensure that Configure::read('debug') is
set to a value greater than 0.

.. php:staticmethod:: Debugger::dump($var)

    Dump prints out the contents of a variable. It will print out all
    properties and methods (if any) of the supplied variable::

        $foo = array(1,2,3);

        Debugger::dump($foo);

        // outputs
        array(
            1,
            2,
            3
        )

        // simple object
        $car = new Car();

        Debugger::dump($car);

        // outputs
        Car
        Car::colour = 'red'
        Car::make = 'Toyota'
        Car::model = 'Camry'
        Car::mileage = '15000'
        Car::accelerate()
        Car::decelerate()
        Car::stop()

    .. versionchanged:: 2.1
        In 2.1 forward the output was updated for readability. See
        :php:func:`Debugger::exportVar()`

.. php:staticmethod:: Debugger::log($var, $level = 7)

    Creates a detailed stack trace log at the time of invocation. The
    log() method prints out data similar to that done by
    Debugger::dump(), but to the debug.log instead of the output
    buffer. Note your app/tmp directory (and its contents) must be
    writable by the web server for log() to work correctly.

.. php:staticmethod:: Debugger::trace($options)

    Returns the current stack trace. Each line of the trace includes
    the calling method, including which file and line the call
    originated from.::

        //In PostsController::index()
        pr(Debugger::trace());
        
        //outputs
        PostsController::index() - APP/Controller/DownloadsController.php, line 48
        Dispatcher::_invoke() - CORE/lib/Cake/Routing/Dispatcher.php, line 265
        Dispatcher::dispatch() - CORE/lib/Cake/Routing/Dispatcher.php, line 237
        [main] - APP/webroot/index.php, line 84

    Above is the stack trace generated by calling Debugger::trace() in
    a controller action. Reading the stack trace bottom to top shows
    the order of currently running functions (stack frames). In the
    above example, index.php called Dispatcher::dispatch(), which
    in-turn called Dispatcher::\_invoke(). The \_invoke() method then
    called PostsController::index(). This information is useful when
    working with recursive operations or deep stacks, as it identifies
    which functions are currently running at the time of the trace().

.. php:staticmethod:: Debugger::excerpt($file, $line, $context)

    Grab an excerpt from the file at $path (which is an absolute
    filepath), highlights line number $line with $context number of
    lines around it.::

        pr(Debugger::excerpt(ROOT . DS . LIBS . 'debugger.php', 321, 2));
        
        //will output the following.
        Array
        (
            [0] => <code><span style="color: #000000"> * @access public</span></code>
            [1] => <code><span style="color: #000000"> */</span></code>
            [2] => <code><span style="color: #000000">    function excerpt($file, $line, $context = 2) {</span></code>
         
            [3] => <span class="code-highlight"><code><span style="color: #000000">        $data = $lines = array();</span></code></span>
            [4] => <code><span style="color: #000000">        $data = @explode("\n", file_get_contents($file));</span></code>
        )

    Although this method is used internally, it can be handy if you're
    creating your own error messages or log entries for custom
    situations.

.. php:staticmethod:: Debugger::exportVar($var, $recursion = 0)

    Converts a variable of any type to a string for use in debug
    output. This method is also used by most of Debugger for internal
    variable conversions, and can be used in your own Debuggers as
    well.

    .. versionchanged:: 2.1
        This function generates different output in 2.1 forward.

.. php:staticmethod:: Debugger::invoke($debugger)

    Replace the CakePHP Debugger with a new instance.

.. php:staticmethod:: Debugger::getType($var)

    Get the type of a variable. Objects will return their class name

    .. versionadded:: 2.1

Using Logging to debug
======================

Logging messages is another good way to debug applications, and you can use
:php:class:`CakeLog` to do logging in your application. All objects that 
extend :php:class:`Object` have an instance method `log()` which can be used
to log messages::

    $this->log('Got here', 'debug');

The above would write ``Got here`` into the debug log. You can use log entries
to help debug methods that involve redirects or complicated loops. You can also
use :php:meth:`CakeLog::write()` to write log messages. This method can be called
statically anywhere in your application one CakeLog has been loaded::

    // in app/Config/bootstrap.php
    App::uses('CakeLog', 'Log');

    // Anywhere in your application
    CakeLog::write('debug', 'Got here');

Debug Kit
=========

DebugKit is a plugin that provides a number of good debugging tools. It primarily
provides a toolbar in the rendered HTML, that provides a plethora of information about 
your application and the current request. You can download 
`DebugKit <https://github.com/cakephp/debug_kit>`_ from github.


.. meta::
    :title lang=en: Debugging
    :description lang=en: Debugging CakePHP with the Debugger class, logging, basic debugging and using the DebugKit plugin.
    :keywords lang=en: code excerpt,stack trace,default output,error link,default error,web requests,error report,debugger,arrays,different ways,excerpt from,cakephp,ide,options
