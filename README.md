# Guidelines

## General
  ### Indentation
  Soft: 2 Spaces

  ### Line break
  UNIX like: LF

  ### Encoding
  UTF-8

  ### EOF
  Always end with a Blank Line

  ### Semicolons
  Nope

## References
  Use `const` for all of your references; avoid using `var`.
  > Why? This ensures that you can’t reassign your references, which can lead to bugs and difficult to comprehend code.

  ```javascript
  // bad
  var a = 1

  // good
  const a = 1
  ```

  If you must reassign references, use `let` instead of `var`.
  > Why? `let` is block-scoped rather than function-scoped like `var`.

  ```javascript
  // bad
  var count = 1
  if (true) {
    count += 1
  }

  // good, use the let.
  let count = 1
  if (true) {
    count += 1
  }
  ```

## Declarative and Functional
  The code should simply describe how it does and not what it uses to do it.

  ```javascript
  // bad
  const greetUser = userId => {
    const user = getUser(userId)
    return 'Hello ${user.name}'
  }

  // good
  const greet = name => `Hello ${name}`
  const greetUser = flow(
    getUser,
    greet,
  )
  ```

  ```javascript
  // bad
  const deleteFileMentions = async channelId => {
    const messages = await getHistory({ channelId })
    const fileMentions = filter(flow(get('subtype'), eq('file_share')))(messages)
    return map(({ ts }) =>
      deleteMessage({ ts, channelId }))(fileMentions))
  }

  // good
  const isFileShare = m => m.subtype === 'file_share'

  const deleteFileMentions = async channelId => {
    const deleteFileMessages = flow(
      filter(isFileShare),
      map(({ ts }) => deleteMessage({ channel: channelId, ts })),
    )
    return getHistory({ channelId })
      .then(deleteFileMessages)
  }
  ```

## One thought per line.
  Keep to one call or assignment per line.
  > Why? It is easy to understand and read through the code.

  ```javascript
  // bad
  const a = `Hello ${await getWorld()}`
  const b = get('name')(await fetchArray())

  // good
  const world = await getWorld()
  const a = `Hello ${world}`
  const b =
    await fetchArray()
      .then(get('name'))
  ```

## Write self-documenting code, avoid using comments
  We're writing code for humans, if we wanted to write for machines we would be using C.
  Functions should have name compatible with their arguments and with what they do.
  > Why? If the code is not explicitly telling what it does, a comment won't help.

  ```javascript
  // bad
  /**
   * Deletes message from a channel.
   * @param {string} id - ID of the channel
   * @param {string} ts - Timestamp of the message
   */
  const delete = (id, ts) => slack.chat.delete({ channel: id, ts })

  // good
  const deleteMessageFromChannel => (channel, message) =>
    slack.chat.delete({ channel: channel.id, ts: message.ts })
  ```

