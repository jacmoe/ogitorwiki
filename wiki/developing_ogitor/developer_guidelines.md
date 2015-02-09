<!--
title: Developer Guidelines
template: wiki
order: 51
-->
### Link commit to Ogitor Tracker ticket
Add the following to your commit message to link your commit to a ticket:
```text
refs #XXX
```
If instead of "refs", you use "fixes", the ticket also gets set to completion status = 100%.
```text
fixes #XXX
```
XXX of course needs to be replaced by the actual ticket number in both cases.