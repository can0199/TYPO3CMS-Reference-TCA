.. include:: /Includes.rst.txt
.. _columns-check-properties-items:

=====
items
=====

.. confval:: items

   :Path: $GLOBALS['TCA'][$table]['columns'][$field]['config']
   :type: array
   :Scope: Display

   If set, this array will create an array of checkboxes instead of just a single "on/off" checkbox.

   .. note::
      You can have a maximum of 31 checkboxes in such an array and each element is represented by a single bit
      in the integer value which ultimately goes into the database.

   In this array each entry is itself an associative array.
   The value sent to the database will be an integer representing a bit mask based on the position of the checkbox
   in this array.

   A basic item looks like this:

   .. code-block:: php

      'items' => [
         ['label' => 'Green tomatoes'], // Note these should be LLL references
         ['label' => 'Red peppers'],
      ],

   .. deprecated:: 12.3

      Using the numerical index :php:`0` for setting the label is deprecated.
      Use the newly introduced :php:`label` key.

   Further properties can be set per item, but not all of them apply to all renderTypes:

   label (string or LLL reference)
      The displayed title.

   invertStateDisplay (boolean)
      All renderTypes. If set to true, checked / unchecked state are swapped in view: A checkbox is marked checked if
      the database bit is *not* set and vice versa.

   iconIdentifierChecked (string)
      Only if renderType is not set (default): An optional icon shown is selected / on. If not set, a check mark
      icon is used.

   iconIdentifierUnchecked (string)
      Only if renderType is not set (default): An optional icon shown selected / on. If not set, no icon is
      show (check mark icon not displayed).

   labelChecked (string)
      Mandatory property for renderType `checkboxLabeledToggle`: Text shown if element is selected / on.

   labelUnchecked (string)
      Mandatory property for renderType `checkboxLabeledToggle`: Text shown if element is not selected.

.. _tca_example_checkbox_3:

Examples
========

.. include:: /Images/Rst/Checkbox3.rst.txt
.. include:: /CodeSnippets/Checkbox3.rst.txt
