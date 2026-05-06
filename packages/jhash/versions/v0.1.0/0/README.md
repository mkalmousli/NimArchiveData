# Jenkins Hasher

This hasher is handy for producing 32 bit identifiers. 

For impementation details and reasoning see the
[Jenkins Hasher](//burtleburtle.net/bob/hash/doobs.html) article in the
*Dr. Doobs* magazine.

The current implementation is taken from the Perl __Digest::JHash__ xs code
and tested against this module (see __Digest::JHash(3pm)__ POSIX manual.)

## Example

       import
         jhash/jhash

       var blurb =
         "How much wood could a woodchuck chuck. "   &
         "If a woodchuck could chuck wood? "         &
         "As much wood as a woodchuck could chuck, " &
         "If a woodchuck could chuck wood."

       doAssert blurb.jHash == 0x61010c8u32
