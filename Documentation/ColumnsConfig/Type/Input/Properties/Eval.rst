.. include:: /Includes.rst.txt
.. _columns-input-properties-eval:

====
eval
====

.. confval:: eval

   :Path: $GLOBALS['TCA'][$table]['columns'][$field]['config']
   :type: string (list of keywords)
   :Scope: Display  / Proc.

   Configuration of field evaluation.

   Some of these evaluation keywords will trigger a JavaScript pre- evaluation in the form. Other evaluations will be
   performed in the backend. The evaluation functions will be executed in the list-order. Keywords:

   alpha
      Allows only a-zA-Z characters.

   alphanum
      Same as "alpha" but allows also "0-9"

   alphanum_x
      Same as "alphanum" but allows also "\_" and "-" chars.

   domainname
      Allows a domain name such as :samp:`example.org` and automatically transforms
      the value to `punicode <https://en.wikipedia.org/wiki/Punycode>`_ if needed.

   is\_in
      Will filter out any character in the input string which is  *not* found in the string entered in the
      property :ref:`is\_in <columns-input-properties-is-in>`.

   lower
      Converts the string to lowercase (only A-Z plus a selected set of Western European special chars).

   md5
      Will convert the input value to its md5-hash using a JavaScript function.

   nospace
      Removes all occurrences of space characters (:php:`chr(32)`)

   num
      Allows only 0-9 characters in the field.

   trim
      The value in the field will have white spaces around it trimmed away.

   unique
      Requires the field to be unique for the *whole* table. Evaluated on the server only.

      .. note::
         When selecting on unique-fields, make sure to select using :code:`AND pid>=0` since the field *can* contain
         duplicate values in other versions of records (always having PID = -1). This also means that if you are using
         versioning on a table where the unique-feature is used you cannot set the field to be truly unique
         in the database either!

   uniqueInPid
      Requires the field to be unique for the current PID among other records on the same page.
      Evaluated on the server only.

   upper
      Converts to uppercase (only A-Z plus a selected set of Western European special chars).

   year
      Evaluates the input to a year between 1970 and 2038. If you need any year, then use "int" evaluation instead.

   Vendor\\Extension\\*
      User defined form evaluations.

   .. deprecated:: 12.0
      The keyword `required` is deprecated. Use the common property
      :confval:`required` instead.

   .. deprecated:: 12.0
      The keyword `null` is deprecated. Use the property
      :ref:`columns-input-properties-nullable` instead.

   .. deprecated:: 12.0
      The keyword `email` is deprecated. Use the column type
      :ref:`columns-email` instead.

   .. deprecated:: 12.0
      The keyword `password` and `saltedPassword` are deprecated.
      Use the column type :ref:`columns-password` instead.

   .. deprecated:: 12.0
      The keywords `int` and `double2` are deprecated. Use the column
      type :ref:`columns-number` instead.

Examples
========

Trim white space
----------------

Trimming the value for white space before storing in the database:

.. code-block:: php

   'aField' => [
      'label' => 'aLabel',
      'config' => [
         'type' => 'input',
         'eval' => 'trim',
      ],
   ],


Combine eval rules
------------------

By this configuration the field will be stripped for any space characters, converted to lowercase, only accepted
if filled in and on the server the value is required to be unique for all records from this table:

.. code-block:: php

    'eval' => 'nospace,lower,unique'


Custom eval rules
-----------------

You can supply own form evaluations in an extension by creating a class with three functions, one which returns
the JavaScript code for client side validation called `returnFieldJS()` and two for the server side:
`deevaluateFieldValue()` called when opening the record and `evaluateFieldValue()` called for validation when
saving the record:

:file:`EXT:extension/Classes/Evaluation/ExampleEvaluation.php`

.. code-block:: php

   namespace Vendor\Extension\Evaluation;

   /**
    * Class for field value validation/evaluation to be used in 'eval' of TCA
    */
   class ExampleEvaluation
   {

      /**
       * JavaScript code for client side validation/evaluation
       *
       * @return string JavaScript code for client side validation/evaluation
       */
      public function returnFieldJS()
      {
         return 'return value + " [added by JavaScript on field blur]";';
      }

      /**
       * Server-side validation/evaluation on saving the record
       *
       * @param string $value The field value to be evaluated
       * @param string $is_in The "is_in" value of the field configuration from TCA
       * @param bool $set Boolean defining if the value is written to the database or not.
       * @return string Evaluated field value
       */
      public function evaluateFieldValue($value, $is_in, &$set)
      {
         return $value . ' [added by PHP on saving the record]';
      }

      /**
       * Server-side validation/evaluation on opening the record
       *
       * @param array $parameters Array with key 'value' containing the field value from the database
       * @return string Evaluated field value
       */
      public function deevaluateFieldValue(array $parameters)
      {
         return $parameters['value'] . ' [added by PHP on opening the record]';
      }
   }

.. code-block:: php
   :caption: EXT:extension/ext_localconf.php

   use Vendor\Extension\Evaluation\ExampleEvaluation;

   // ....

   // Register the class to be available in 'eval' of TCA
   $GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['tce']['formevals'][ExampleEvaluation::class] = '';

.. code-block:: php
   :caption: EXT:extension/Configuration/TCA/tx_example_record.php

   'columns' => [
      'example_field' => [
         'config' => [
            'type' => 'text',
            'required' => true,
            'eval' => 'trim,' . \Vendor\Extension\Evaluation\ExampleEvaluation::class
         ],
      ],
   ],
