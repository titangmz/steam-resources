# node-steam-resources

This is a node wrapper around Steam resources ([Protobufs](https://github.com/SteamDatabase/Protobufs) and [SteamLanguage](https://github.com/SteamRE/SteamKit/tree/master/Resources/SteamLanguage)). It's primarily intended for [node-steam](https://github.com/seishun/node-steam) extension module authors.

# Installation

Run `npm install @titangmz/steam-resources`.

# Usage

```js
var Steam = require('steam-resources');
```

`Steam` is now a namespace object containing enums and classes generated from the protobufs and SteamLanguage.

# Enums

For each enum in SteamLanguage, there is an equivalently named property on `Steam`. The property is an object; for each of the enum's members, there is an equivalently named property on the object with an equivalent Number value.

For example, `Steam.EClanPermission.OwnerOfficerModerator` is equal to `11`.

# Protobufs

For each protobuf message or enum available in SteamKit2, there is an equivalently named class or enum generated using [ProtoBuf.js](https://github.com/dcodeIO/ProtoBuf.js) version [^4.1](https://docs.npmjs.com/misc/semver#caret-ranges-123-025-004). They lie in the same hierarchy as in SteamKit2, with objects for namespaces. (If you see a mismatch, consider that a bug.)

For example, the `CMsgGCTopCustomGamesList` message from [dota_gcmessages_common.proto](https://github.com/SteamDatabase/Protobufs/blob/master/dota2/dota_gcmessages_common.proto) is available as `SteamKit2.GC.Dota.Internal.CMsgGCTopCustomGamesList` in SteamKit2 and as `Steam.GC.Dota.Internal.CMsgGCTopCustomGamesList` here.

# Structs

For each class in SteamLanguage, there is an equivalently named class in `Steam.Internal`. They are intended to implement a subset of the API provided by ProtoBuf.js message classes. Namely, for a class `MsgGabe`:

* An `MsgGabe` instance has an equivalently named property for each non-const member of `MsgGabe` with the type as follows (modifiers like `boolmarshal` are ignored):
  * byte<> members: [ByteBuffer.js](https://github.com/dcodeIO/ByteBuffer.js) ^5.0 objects
  * `long` and `ulong` members: [ByteBuffer.Long](https://github.com/dcodeIO/Long.js) objects
  * Other numeric members: Number
  * Protobuf message members: message class instances
* For each const member of `MsgGabe`, there is an equivalently named property on the `MsgGabe` class with an equivalent value.
* `MsgGabe.decode(buf)` returns an `MsgGabe` instance deserialized from `buf`. `buf` can be either a Buffer or a ByteBuffer.js ^5.0 instance. In the latter case, it decodes starting from `buf.offset` and increments it by message size.
* `new MsgGabe(obj)` creates an `MsgGabe` instance from an object. `new MsgGabe()` is equivalent to `new MsgGabe({})`. For each non-const member of `MsgGabe`, if there is an equivalently named property in `obj`, it sets the instance property to the (possibly converted) value of the property in `obj`, otherwise to the default value. In addition to the types listed above, `obj` properties can have the following types:
  * byte<> members: Buffer objects (converted using `ByteBuffer.wrap`)
  * Numeric members: String or Number (converted using `Long.fromValue`)
  * Protobuf message members: objects (converted using the message class constructor)
* An `MsgGabe` instance `gabe` has the following methods:
    * `gabe.encode()` serializes the `MsgGabe` instance and returns a ByteBuffer.js ^5.0 object.
    * `gabe.toBuffer()` returns `this.encode().toBuffer()`.

For example, `MsgClientChatRoomInfo` can be used as follows:

```js
var chatRoomInfo = new Steam.Internal.MsgClientChatRoomInfo({
  steamIdChat: '103582791432594962'
});
var buf = chatRoomInfo.toBuffer();
var chatRoomInfo2 = Steam.Internal.MsgClientChatRoomInfo.decode(buf);
```


## Credits
https://github.com/seishun/node-steam-resources