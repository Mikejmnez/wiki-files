Current, a constraint expression is provided per container specified in
a definition. For example (cedar constraint):

This is available in Hyrax 1.7

    <?xml version="1.0" encoding="UTF-8"?>
    <request reqID="some_unique_value" >
        <define name="d">
        <container name="mfp920504a">
                <constraint>date(2002,110,0,0,2002,120,2359,5999)</constraint>
            </container>
        <container name="mfp920504b">
                <constraint>date(2002,110,0,0,2002,120,2359,5999)</constraint>
            </container>
        <container name="mfp920504c">
                <constraint>date(2002,110,0,0,2002,120,2359,5999)</constraint>
            </container>
        </define>
        <get type="tab" definition="d" />
    </request>

Notice that the constraints are the same for each container. In an xml
document, this isn't too much of a problem. But with the current apache
module for the CEDAR project that constraint is repeated for each
container in the string request. This string request eventually gets
translated into an xml request document. For simplicity sake, we would
like to have the following:

    <?xml version="1.0" encoding="UTF-8"?>
    <request reqID="some_unique_value" >
        <define name="d">
            <constraint>date(2002,110,0,0,2002,120,2359,5999)</constraint>
        <container name="mfp920504a" />
        <container name="mfp920504b" />
            <container name="mfp920504c" />
        </define>
        <get type="tab" definition="d" />
    </request>

In this example, there is a single constraint for all of the containers.

here's the rules:

1.  If there is a single constraint defined in the definition, as in the
    example above, then the constraint gets applied to each of the
    containers.
2.  If there is a constraint defined within the container element, then
    that constraint is used for that container, overriding the
    constraint for the definition if it exists.
3.  If there is an empty constraint for a container, where there is a
    constraint for the definition, this means that there is no
    constraint for that container.

[Single Constraint per Definition](Category:Development "wikilink")
[Single Constraint per
Definition](Category:Hyrax_Development "wikilink")