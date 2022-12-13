# ActivityPub URI Schema Proposal

This is a Draft proposal of URI schema meant to be used across fedirated applications using ActivityPub protocol to improve people's experiance and increase interoperability.

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

Fedirated applications in current state do exchange information effectively among servers if both Actors established a subscription.
Many applications implemented in-app views of links in Fediverse so that people do not have to leave their application and most of them implement Foolow button in such views.
But people might explore web outside of their beloved application or their application may not implement such in-app views and ask people to see pages or posts on original website.
In both cases people are not able to interact with such fedirated content or Actors.
Usually people are forced to copy link to the content or Actor they are interested and paste it inside their application to perform actions from this application.

URI schema in this document is meant to improve people's interaction with content and Actors and to improve applications interoperability. Proposal also tries to make URI schema extensible 

## Schema definition

Schema consists of URI schema `web+activitypub:` prefix, Activity Type and Activity parameters as query parameters.

```
activitypub-schema := "web+activitypub" ":" activity-type ("?" activity-properties)
```
- `activity-type` defines one if Activity Types which is one of:
  * defined in [ActivityStreams Vocabulary](https://www.w3.org/TR/activitystreams-vocabulary/#activity-types) and has no prefix
  * compact IRI Term encoded [TODO: add link] as URI component (e.g. `group:Greet` which will be encoded as `group%3AGreet` given that `group` is _prefix_ and defined with compact IRI in `activity-properties`)
- `activity-properties` is flat key-value list using URI query parameters encoding [TODO: add link] with following structure rules:
  * Keys and values should be used as defined in corresponding [ActivityStream Activity](https://www.w3.org/TR/activitystreams-vocabulary/#activity-types) but Activities and objects should be replaced with it's id.
  * Compact IRI definitions **key** should use `"@context:" prefix` syntax which defines _prefix_ to be used in other , and **value** is URI encoded as URI component [TODO add link].
  * Compact IRI terms keys should use `prefix ":" property` syntax which uses _prefix_ defined in IRI definition earlier and _property_ defined in IRI embeded in definition.

## Support considirations

Any non IRI Term Activity should be interpreted as defined in [ActivityPub Client to Server interaction](https://www.w3.org/TR/activitypub/#client-to-server-interactions).

Every application supporting this proposal is REQUIRED to implement `Follow` Activity Type as [defined in ActivtyPub](https://www.w3.org/TR/activitypub/#follow-activity-outbox).

**TODO: define Announce activity handling**

## Examples

### Follow activity URI
```
web+activitypub:Follow?object=https%3A%2F%2Fmastodon.ml%2Fusers%2Fbano
```
Given URI asks application to follow Actor with id `https://mastodon.ml/users/bano`.


```
web+activitypub:Follow?object=acct%3Abano%40mastodon.ml
```
URI asks application to follow Actor with `acct:bano@mastodon.ml` which will be resolved to `https://mastodon.ml/users/bano` using WebFinger.

## History

<!-- new entries should be added below this comment line -->
- 2022-12-13 @bano@mastodon.ml - Wrote first draft version
