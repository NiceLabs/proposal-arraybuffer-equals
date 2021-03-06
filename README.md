# proposal-arraybuffer-equal

This is a proposal to add a new method, `ArrayBuffer.isEqual(a: ArrayBuffer, b: ArrayBuffer)`,
to JavaScript's `ArrayBuffer` class.

It has not yet been presented to the JavaScript standards committee.

## Need discussion

- [ ] Does it need to be extended to all TypedArray?
- [ ] Not expected input type, _throw TypeError_ or returns false?

## The problem

```typescript
const encoder = new TextEncoder();
const input = 'sample';
const a = encoder.encode(input);
const b = encoder.encode(input);
console.log(a === b); // returns false
console.log(a == b); // returns false
```

If the re-definition [Abstract Equality Comparison](https://tc39.es/ecma262/#sec-abstract-equality-comparison) or [Strict Equality Comparison](https://tc39.es/ecma262/#sec-strict-equality-comparison) is break change behavior.

So we need to **define a new method**.

## How compare two ArrayBuffer object is equality

To solve this problem, you need to define a method.

```typescript
// The is TypeScript code
function isEqual(a: ArrayBuffer, b: ArrayBuffer) {
  if (!(a instanceof ArrayBuffer)) {
    return false;
  } else if (!(b instanceof ArrayBuffer)) {
    return false;
  } else if (a === b) {
    return true;
  } else if (a.byteLength !== b.byteLength) {
    return false;
  }
  const view1 = new DataView(a);
  const view2 = new DataView(b);
  for (let i = 0; i < view1.byteLength; i++) {
    if (view1.getUint8(i) !== view2.getUint8(i)) {
      return false;
    }
  }
  return true;
}
```

## Polyfill

Wait for enter stage 1, then publish to npm registry.

```bash
# the is draft stage version (UNSTABLE)
npm install @nicelabs/proposal-arraybuffer-equals --registry https://npm.septs.me
```

## `ArrayBuffer.isEqual`

To do this, we propose a new method, `ArrayBuffer.isEqual(a, b)`, which compare two array buffer is equality (bit-wise)

```typescript
// returns false
ArrayBuffer.isEqual(Uint8Array.of(0).buffer, undefined);
// returns false
ArrayBuffer.isEqual(Uint8Array.of(0).buffer, null);
// returns false
ArrayBuffer.isEqual(Uint8Array.of(0).buffer, Uint8Array.of(0, 1).buffer);
// returns true
ArrayBuffer.isEqual(Uint8Array.of(0).buffer, Uint8Array.of(0).buffer);
// returns true
ArrayBuffer.isEqual(
  Uint32Array.from([Number.MAX_SAFE_INTEGER]).buffer,
  Uint8Array.of(255, 255, 255, 255).buffer,
);
```

## Related discussion

- <https://es.discourse.group/t/compare-two-arraybuffers-bit-wise-for-equality/413>
