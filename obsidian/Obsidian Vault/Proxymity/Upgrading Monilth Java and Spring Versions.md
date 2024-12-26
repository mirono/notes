Trying to compile the project wit hJava 11, gives the following issues:
java: as of release 10, 'var' is a restricted local variable type and cannot be used for type declarations or as the element type of an array

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/utils/StringUtils.java:19:46
java: package sun.security.krb5.internal.ktab does not exist

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/utils/StringUtils.java:19
java: static import only from classes and interfaces

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/utils/DateTimeUtils.java:6:56
java: package com.sun.org.apache.xerces.internal.jaxp.datatype does not exist

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/intermediary/Dto/IntermediaryDto.java:18:20
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/entity/LeanBeneficialOwner.java:33:21
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:53:18
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:55:18
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:57:29
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:59:31
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:63:20
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:67:20
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:123:18
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:125:27
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:129:18
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/utils/Triplet.java:7
java: Not generating hashCode: One of equals or hashCode exists. You should either write both of these or none of these (in the latter case, lombok generates them).

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/utils/DateTimeUtils.java:6:56
java: package com.sun.org.apache.xerces.internal.jaxp.datatype does not exist
