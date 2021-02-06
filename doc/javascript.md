# JavaScript Extensions for Carnap

## Including JavaScript

Carnap allows you to incorporate external JavaScript to an assignment, in order
to add extra interactive behavior to your problem sets. This can be done using
the `raw_html` pandoc extension to include a `<script>` tag, or by including
the JS in a metadata block. Like a CSS entry, a JS entry entry can include
either a url for a single CSS stylesheet, like so:

    ---
    js: https://carnap.io/shared/myemail@university.edu/myjs.js
    --- 

or for several stylesheets, like so:

    ---
    js:
    - https://carnap.io/shared/myemail@university.edu/myjs1.js
    - https://carnap.io/shared/myemail@university.edu/myjs2.js
    --- 

The scripts can be hosted anywhere, including on the Carnap server. As with
stylesheets, scripts can be uploaded as normal documents so long as they're
given the proper filetype extension (in this case, ".js" rather than ".css")

## Advanced Usage: Interacting with the Server

JavaScript running as part of an assignment will have access to an object
called `CarnapServerAPI`, which lets your JavaScript interact with the server,
to retrieve information about the student and the current assignment, and to
save information on the server for later retrieval.

Currently, the useful properties of `CarnapServerAPI` are as follows:

<div class="table">

Property                                   Data
------------------------------------------ ----------------
`CarnapServerAPI.user.firstName`           Student's first name
`CarnapServerAPI.user.lastName`            Student's first name
`CarnapServerAPI.assignment.dueDate`       Assignment due date ECMAScript epoch time (milliseconds since midnight 1/1/1970 UTC, for use with the [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) constructor).
`CarnapServerAPI.assignment.description`   Assignment description 
------------------------------------------ ----------------

</div>

There are two methods for dealing with saving data, one for saving and one for
retrieving. Data is always saved under a "namespace", so that different
JavaScript extensions can avoid overwriting one another's saved information.
Each student has their own saved state for each assignment - state is not
shared between students or between assignments.

<div class="table">

Method                                         Result
---------------------------------------------- ----------------
`CarnapServerAPI.putAssignmentState(ns,state)` Serializes `state` and inserts it under namespace `ns` on the server.
`CarnapServerAPI.getAssignmentState()`         Retrieves the assignment state asynchronously, as a [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise).
---------------------------------------------- ----------------

</div>

The first call to `CarnapServerAPI.getAssignmentState` retrieves the state
from the server, and subsequent calls return a locally cached state that's
updated by `CarnapServerAPI.putAssignmentState`. So, while the first call to
`CarnapServerAPI.getAssignmentState` might take a moment, subsequent calls
should be quite fast.