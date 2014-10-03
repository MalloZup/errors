
# errors
    import "github.com/juju/errors"

The juju/errors provides an easy way to annotate errors without losing the
orginal error context.

The package is based on github.com/juju/errgo and embeds the errgo.Err type.

The exported New and Errorf functions is designed to replace the errors.New
and fmt.Errorf functions respectively. The same underlying error is there, but
the package also records the location at which the error was created.

A primary use case for this library is to add extra context any time an
error is returned from a function.


	    if err := SomeFunc(); err != nil {
		    return err
		}

This instead becomes:


	    if err := SomeFunc(); err != nil {
		    return errors.Trace(err)
		}

which just records the file and line number of the Trace call, or


	    if err := SomeFunc(); err != nil {
		    return errors.Annotate(err, "more context")
		}

which also adds an annotation to the error.

Often when you want to check to see if an error is of a particular type, a
helper function is exported by the package that returned the error, like the
`os` package.  The underlying cause of the error is available using the
Cause function, or you can test the cause with the Check function.


	os.IsNotExist(errors.Cause(err))
	
	errors.Check(err, os.IsNotExist)

The result of the Error() call on the annotated error is the annotations
joined with colons, then the result of the Error() method
for the underlying error that was the cause.


	err := errors.Errorf("original")
	err = errors.Annotatef("context")
	err = errors.Annotatef("more context")
	err.Error() -> "more context: context: original"

Obviously recording the file, line and functions is not very useful if you
cannot get them back out again.


	errors.ErrorStack(err)

will return something like:


	first error
	github.com/juju/errors/annotation_test.go:193:
	github.com/juju/errors/annotation_test.go:194: annotation
	github.com/juju/errors/annotation_test.go:195:
	github.com/juju/errors/annotation_test.go:196: more context
	github.com/juju/errors/annotation_test.go:197:

The first error was generated by an external system, so there was no location
associated. The second, fourth, and last lines were generated with Trace calls,
and the other two through Annotate.

If you are creating the errors, you can simply call:


	errors.Errorf("format just like fmt.Errorf")

This function will return an error that contains the annotation stack and
records the file, line and function from the place where the error is created.

Sometimes when responding to an error you want to return a more specific error
for the situation.


	    if err := FindField(field); err != nil {
		    return errors.Wrap(err, errors.NotFoundf(field))
		}

This returns an error where the complete error stack is still available, and
errors.Cause will return the NotFound error.






## func AlreadyExistsf
``` go
func AlreadyExistsf(format string, args ...interface{}) error
```
AlreadyExistsf returns an error which satisfies IsAlreadyExists().


## func Annotate
``` go
func Annotate(other error, message string) error
```
Annotate is used to add extra context to an existing error. The location of
the Annotate call is recorded with the annotations. The file, line and
function are also recorded.

For example:


	if err := SomeFunc(); err != nil {
	    return errors.Annotate(err, "failed to frombulate")
	}


## func Annotatef
``` go
func Annotatef(other error, format string, args ...interface{}) error
```
Annotatef is used to add extra context to an existing error. The location of
the Annotate call is recorded with the annotations. The file, line and
function are also recorded.

For example:


	if err := SomeFunc(); err != nil {
	    return errors.Annotatef(err, "failed to frombulate the %s", arg)
	}


## func Cause
``` go
func Cause(err error) error
```
Cause returns the cause of the given error.  If err does not implement
errgo.Causer or its Cause method returns nil, it returns err itself.


## func Check
``` go
func Check(err error, checker func(error) bool) bool
```
Check looks at the Cause of the error to see if it matches the checker
function.

For example:


	if err := SomeFunc(); err != nil {
	    if errors.Check(err, os.IsNotExist) {
	        return someOtherFunc()
	    }
	}


## func Contextf
``` go
func Contextf(err *error, format string, args ...interface{})
```
Contextf prefixes any error stored in err with text formatted
according to the format specifier. If err does not contain an
error, Contextf does nothing. All errors created with functions
from this package are preserved when wrapping.


## func ErrorStack
``` go
func ErrorStack(err error) string
```
ErrorStack returns a string representation of the annotated error. If the
error passed as the parameter is not an annotated error, the result is
simply the result of the Error() method on that error.

If the error is an annotated error, a multi-line string is returned where
each line represents one entry in the annotation stack. The full filename
from the call stack is used in the output.


## func Errorf
``` go
func Errorf(format string, args ...interface{}) error
```
Errorf creates a new annotated error and records the location that the
error is created.  This should be a drop in replacement for fmt.Errorf.

For example:


	return errors.Errorf("validation failed: %s", message)


## func IsAlreadyExists
``` go
func IsAlreadyExists(err error) bool
```
IsAlreadyExists reports whether the error was created with
AlreadyExistsf() or NewAlreadyExists().


## func IsNotFound
``` go
func IsNotFound(err error) bool
```
IsNotFound reports whether err was created with NotFoundf() or
NewNotFound().


## func IsNotImplemented
``` go
func IsNotImplemented(err error) bool
```
IsNotImplemented reports whether err was created with
NotImplementedf() or NewNotImplemented().


## func IsNotSupported
``` go
func IsNotSupported(err error) bool
```
IsNotSupported reports whether the error was created with
NotSupportedf() or NewNotSupported().


## func IsNotValid
``` go
func IsNotVa;od(err error) bool
```
IsNotValid reports whether the error was created with NotValidf() or
NewNotValid().


## func IsUnauthorized
``` go
func IsUnauthorized(err error) bool
```
IsUnauthorized reports whether err was created with Unauthorizedf() or
NewUnauthorized().


## func LoggedErrorf
``` go
func LoggedErrorf(logger loggo.Logger, format string, a ...interface{}) error
```
LoggedErrorf logs the error and return an error with the same text.


## func Maskf
``` go
func Maskf(err *error, format string, args ...interface{})
```
Maskf masks the given error (when it is not nil) with the given
format string and arguments (like fmt.Sprintf), returning a new
error. If *err is nil, Maskf does nothing.


## func New
``` go
func New(message string) error
```
New is a drop in replacement for the standard libary errors module that records
the location that the error is created.

For example:


	return errors.New("validation failed")


## func NewAlreadyExists
``` go
func NewAlreadyExists(err error, msg string) error
```
NewAlreadyExists returns an error which wraps err and satisfies
IsAlreadyExists().


## func NewNotFound
``` go
func NewNotFound(err error, msg string) error
```
NewNotFound returns an error which wraps err that satisfies
IsNotFound().


## func NewNotImplemented
``` go
func NewNotImplemented(err error, msg string) error
```
NewNotImplemented returns an error which wraps err and satisfies
IsNotImplemented().


## func NewNotSupported
``` go
func NewNotSupported(err error, msg string) error
```
NewNotSupported returns an error which wraps err and satisfies
IsNotSupported().


## func NewNotValid
``` go
func NewNotValid(err error, msg string) error
```
NewNotValid returns an error which wraps err and satisfies IsNotValid().


## func NewUnauthorized
``` go
func NewUnauthorized(err error, msg string) error
```
NewUnauthorized returns an error which wraps err and satisfies
IsUnauthorized().


## func NotFoundf
``` go
func NotFoundf(format string, args ...interface{}) error
```
NotFoundf returns an error which satisfies IsNotFound().


## func NotImplementedf
``` go
func NotImplementedf(format string, args ...interface{}) error
```
NotImplementedf returns an error which satisfies IsNotImplemented().


## func NotSupportedf
``` go
func NotSupportedf(format string, args ...interface{}) error
```
NotSupportedf returns an error which satisfies IsNotSupported().


## func NotValidf
``` go
func NotValidf(format string, args ...interface{}) error
```
NotValidf returns an error which satisfies IsNotValid().


## func Trace
``` go
func Trace(other error) error
```
Trace always returns an annotated error.  Trace records the
location of the Trace call, and adds it to the annotation stack.

For example:


	if err := SomeFunc(); err != nil {
	    return errors.Trace(err)
	}


## func Unauthorizedf
``` go
func Unauthorizedf(format string, args ...interface{}) error
```
Unauthorizedf returns an error which satisfies IsUnauthorized().


## func Wrap
``` go
func Wrap(other, newDescriptive error) error
```
Wrap changes the error value that is returned with LastError. The location
of the Wrap call is also stored in the annotation stack.

For example:


	if err := SomeFunc(); err != nil {
	    newErr := &packageError{"more context", private_value}
	    return errors.Wrap(err, newErr)
	}



## type Err
``` go
type Err struct {
    errgo.Err
}
```
A juju Err is an errgo.Err but the error string generated walks up the
stack of errors adding the messages but stops if the cause of the
underlying error is different.











### func (\*Err) Error
``` go
func (e *Err) Error() string
```
Error implements error.Error.









- - -
Generated by [godoc2md](http://godoc.org/github.com/davecheney/godoc2md)
