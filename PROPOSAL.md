# ActivityPub URI Schema Proposal

This is a Draft proposal of URI schema meant to be used across federated applications using ActivityPub protocol to improve people's experience and increase interoperability.

## Definitions

<dl>

  <dt>Fediverse</dt>
  <dd>
    A network of interconnected applications using ActivityPub protocol to exchange information.
  </dd>

  <dt>Actor</dt>
  <dd>
    <a href="https://www.w3.org/TR/activitystreams-vocabulary/#actor-types">Defined</a> in ActivityStreams Vocabulary.
  </dd>
</dl>



## Rationale

Federated applications in current state do exchange information effectively among servers if both Actors established a subscription.
Many applications implemented in-app views of links in Fediverse so that people do not have to leave their application and most of them implement Follow button in such views.
But people might explore web outside of their beloved application or their application may not implement such in-app views and ask people to see pages or posts on original website.
In both cases people are not able to interact with such federated content or Actors.
Usually people are forced to copy link to the content or Actor they are interested and paste it inside their application to perform actions from this application.

URI schema in this document is meant to improve people's interaction with content and Actors and to improve applications interoperability. Proposal also tries to make URI schema extensible 

## Schema definition

Schema consists of URI schema `web+activitypub:` prefix, Activity Type and Activity parameters as query parameters.

```
activitypub-schema := "web+activitypub" ":" activity-type "?" activity-properties
```
- `activity-type` defines one if Activity Types which is one of:
  * defined in [ActivityStreams Vocabulary](https://www.w3.org/TR/activitystreams-vocabulary/#activity-types) and has no prefix
  * compact IRI Term encoded [TODO: add link] as URI component (e.g. `group:Greet` which will be encoded as `group%3AGreet` given that `group` is _prefix_ and defined with compact IRI in `activity-properties`)
- `activity-properties` is flat key-value list using URI query parameters encoding [TODO: add link] with following structure rules:
  * Keys and values should be used as defined in corresponding [ActivityStream Activity](https://www.w3.org/TR/activitystreams-vocabulary/#activity-types) but Activities and objects should be replaced with it's id.
  * Compact IRI Definitions **key** should use `"@context:" prefix` syntax which defines _prefix_ to be used in other properties and `activity-type`, and **value** is URI encoded as URI component [TODO add link].
  * Compact IRI Terms keys should use `prefix ":" property` syntax which uses _prefix_ defined in IRI definition earlier and _property_ defined in IRI referenced in definition.

IRI Definition key-value pairs SHOULD be listed before other key-value pairs in `activity-properties`

## URI Schema consuming

Application MUST handle `object` property in every supported Activity.
Application MAY skip handling any properties it doesn't support.

### ActivityPub Activities

Application consuming described URI schema MUST implement `Follow` Activity Type as [defined in ActivityPub](https://www.w3.org/TR/activitypub/#follow-activity-outbox).

Application MAY consume Client Activities defined in [ActivityPub Client to Server interaction](https://www.w3.org/TR/activitypub/#client-to-server-interactions).

### `Announce` Activity

Application MAY consume type `Announce` with properties [defined in ActivityStreams Vocabulary](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-announce).
Application MUST interpret `Announce` as a command to announce content referenced in `object` property.

### Extended Activities and properties

Application MAY support additional Activity Types and property extensions defined in compact IRI definitions.
Application MUST check support of IRI-defined activities and properties only by IRI and not by prefix.

## Examples

### `Follow` Activity URI

```
web+activitypub:Follow?object=https%3A%2F%2Fmastodon.ml%2Fusers%2Fbano
```
This URI represents following Activity:
```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "type": "Follow",
  "object": "https://mastodon.ml/users/bano"
}
```
Given Activity asks application to follow Actor with id `https://mastodon.ml/users/bano`.


```
web+activitypub:Follow?object=acct%3Abano%40mastodon.ml
```
This URI represents following Activity:
```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "type": "Follow",
  "object": "acct:bano@mastodon.ml"
}
```
URI asks application to follow Actor with `acct:bano@mastodon.ml` which will be resolved to `https://mastodon.ml/users/bano` using WebFinger.

### `Announce` Activity URI

```
web+activitypub:Announce?object=https%3A%2F%2Fexample.org%2Fstatus%2Fcat-greeting
```
This URI represents following Activity:
```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "type": "Announce",
  "object": "https://example.org/status/cat-greeting"
}
```
Which asks application to announce (share/boost/repost) `cat-greeting` Activity.

### Extended Activities

> NOTE: URIs in this section are split in lines to be readable.

Next URI shows how to use extended Activity Types and extended properties
```
web+activitypub:cat%3AHug?
%40context%3Acat=https%3A%2F%2Fexample.com%2Fcat-lovers%23&
object=https%3A%2F%2Fexample.org%2Fstatus%2Fcat-greeting&
cat%3Aname=Snowball
```
After parsing URI we can get data:
```json
{
  "type": "cat:Hug",
  "properties": [
    { "@context:cat": "" },
    { "object": "https://example.org/status/cat-greeting" },
    { "cat:name": "Snowball" }
  ]
}
```

Which should be interpreted as following Activity:
```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    {"cat": "https://example.com/cat-lovers#"}
  ],
  "type": "cat:Hug",
  "object": "https://example.org/status/cat-greeting",
  "cat:name": "Snowball"
}
```





## History

<!-- new entries should be added below this comment line -->
- 2022-12-13 @bano@mastodon.ml - Wrote first draft version
