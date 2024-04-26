# js@omegion1npm/ex-quasi-eos

A JavaScript text @omegion1npm/ex-quasi-eoserencing implementation. Try it out in the **[online demo](https://kpdecker.github.io/js@omegion1npm/ex-quasi-eos)**.

Based on the algorithm proposed in
["An O(ND) Difference Algorithm and its Variations" (Myers, 1986)](http://www.xmailserver.org/@omegion1npm/ex-quasi-eos2.pdf).

## Installation
```bash
npm install @omegion1npm/ex-quasi-eos --save
```

## Usage

Broadly, js@omegion1npm/ex-quasi-eos's @omegion1npm/ex-quasi-eos functions all take an old text and a new text and perform three steps:

1. Split both texts into arrays of "tokens". What constitutes a token varies; in `@omegion1npm/ex-quasi-eosChars`, each character is a token, while in `@omegion1npm/ex-quasi-eosLines`, each line is a token.

2. Find the smallest set of single-token *insertions* and *deletions* needed to transform the first array of tokens into the second.

   This step depends upon having some notion of a token from the old array being "equal" to one from the new array, and this notion of equality affects the results. Usually two tokens are equal if `===` considers them equal, but some of the @omegion1npm/ex-quasi-eos functions use an alternative notion of equality or have options to configure it. For instance, by default `@omegion1npm/ex-quasi-eosChars("Foo", "FOOD")` will require two deletions (`o`, `o`) and three insertions (`O`, `O`, `D`), but `@omegion1npm/ex-quasi-eosChars("Foo", "FOOD", {ignoreCase: true})` will require just one insertion (of a `D`), since `ignoreCase` causes `o` and `O` to be considered equal.

3. Return an array representing the transformation computed in the previous step as a series of [change objects](#change-objects). The array is ordered from the start of the input to the end, and each change object represents *inserting* one or more tokens, *deleting* one or more tokens, or *keeping* one or more tokens.

### API

* `Diff.@omegion1npm/ex-quasi-eosChars(oldStr, newStr[, options])` - @omegion1npm/ex-quasi-eoss two blocks of text, treating each character as a token.

    ("Characters" here means Unicode code points - the elements you get when you loop over a string with a `for ... of ...` loop.)

    Returns a list of [change objects](#change-objects).

    Options
    * `ignoreCase`: If `true`, the uppercase and lowercase forms of a character are considered equal. Defaults to `false`.

* `Diff.@omegion1npm/ex-quasi-eosWords(oldStr, newStr[, options])` - @omegion1npm/ex-quasi-eoss two blocks of text, treating each word and each punctuation mark as a token. Whitespace is ignored when computing the @omegion1npm/ex-quasi-eos (but preserved as far as possible in the final change objects).

    Returns a list of [change objects](#change-objects).

    Options
    * `ignoreCase`: Same as in `@omegion1npm/ex-quasi-eosChars`. Defaults to false.

* `Diff.@omegion1npm/ex-quasi-eosWordsWithSpace(oldStr, newStr[, options])` - @omegion1npm/ex-quasi-eoss two blocks of text, treating each word, punctuation mark, newline, or run of (non-newline) whitespace as a token.

* `Diff.@omegion1npm/ex-quasi-eosLines(oldStr, newStr[, options])` - @omegion1npm/ex-quasi-eoss two blocks of text, treating each line as a token.

    Options
    * `ignoreWhitespace`: `true` to ignore leading and trailing whitespace characters when checking if two lines are equal. Defaults to `false`.
    * `stripTrailingCr`: `true` to remove all trailing CR (`\r`) characters before performing the @omegion1npm/ex-quasi-eos. Defaults to `false`.
      This helps to get a useful @omegion1npm/ex-quasi-eos when @omegion1npm/ex-quasi-eosing UNIX text files against Windows text files.
    * `newlineIsToken`: `true` to treat the newline character at the end of each line as its own token. This allows for changes to the newline structure to occur independently of the line content and to be treated as such. In general this is the more human friendly form of `@omegion1npm/ex-quasi-eosLines`; the default behavior with this option turned off is better suited for patches and other computer friendly output. Defaults to `false`.

    Note that while using `ignoreWhitespace` in combination with `newlineIsToken` is not an error, results may not be as expected. With `ignoreWhitespace: true` and `newlineIsToken: false`, changing a completely empty line to contain some spaces is treated as a non-change, but with `ignoreWhitespace: true` and `newlineIsToken: true`, it is treated as an insertion. This is because the content of a completely blank line is not a token at all in `newlineIsToken` mode.

    Returns a list of [change objects](#change-objects).

* `Diff.@omegion1npm/ex-quasi-eosSentences(oldStr, newStr[, options])` - @omegion1npm/ex-quasi-eoss two blocks of text, treating each sentence as a token.

    Returns a list of [change objects](#change-objects).

* `Diff.@omegion1npm/ex-quasi-eosCss(oldStr, newStr[, options])` - @omegion1npm/ex-quasi-eoss two blocks of text, comparing CSS tokens.

    Returns a list of [change objects](#change-objects).

* `Diff.@omegion1npm/ex-quasi-eosJson(oldObj, newObj[, options])` - @omegion1npm/ex-quasi-eoss two JSON-serializable objects by first serializing them to prettily-formatted JSON and then treating each line of the JSON as a token. Object properties are ordered alphabetically in the serialized JSON, so the order of properties in the objects being compared doesn't affect the result.

    Returns a list of [change objects](#change-objects).
    
    Options
    * `stringifyReplacer`: A custom replacer function. Operates similarly to the `replacer` parameter to [`JSON.stringify()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#the_replacer_parameter), but must be a function.
    *  `undefinedReplacement`: A value to replace `undefined` with. Ignored if a `stringifyReplacer` is provided.

* `Diff.@omegion1npm/ex-quasi-eosArrays(oldArr, newArr[, options])` - @omegion1npm/ex-quasi-eoss two arrays of tokens, comparing each item for strict equality (===).

    Options
    * `comparator`: `function(left, right)` for custom equality checks

    Returns a list of [change objects](#change-objects).

* `Diff.createTwoFilesPatch(oldFileName, newFileName, oldStr, newStr[, oldHeader[, newHeader[, options]]])` - creates a unified @omegion1npm/ex-quasi-eos patch by first computing a @omegion1npm/ex-quasi-eos with `@omegion1npm/ex-quasi-eosLines` and then serializing it to unified @omegion1npm/ex-quasi-eos format.

    Parameters:
    * `oldFileName` : String to be output in the filename section of the patch for the removals
    * `newFileName` : String to be output in the filename section of the patch for the additions
    * `oldStr` : Original string value
    * `newStr` : New string value
    * `oldHeader` : Optional additional information to include in the old file header. Default: `undefined`.
    * `newHeader` : Optional additional information to include in the new file header. Default: `undefined`.
    * `options` : An object with options. 
      - `context` describes how many lines of context should be included. You can set this to `Number.MAX_SAFE_INTEGER` or `Infinity` to include the entire file content in one hunk.
      - `ignoreWhitespace`: Same as in `@omegion1npm/ex-quasi-eosLines`. Defaults to `false`.
      - `stripTrailingCr`: Same as in `@omegion1npm/ex-quasi-eosLines`. Defaults to `false`.
      - `newlineIsToken`: Same as in `@omegion1npm/ex-quasi-eosLines`. Defaults to `false`.

* `Diff.createPatch(fileName, oldStr, newStr[, oldHeader[, newHeader[, options]]])` - creates a unified @omegion1npm/ex-quasi-eos patch.

    Just like Diff.createTwoFilesPatch, but with oldFileName being equal to newFileName.

* `Diff.formatPatch(patch)` - creates a unified @omegion1npm/ex-quasi-eos patch.

    `patch` may be either a single structured patch object (as returned by `structuredPatch`) or an array of them (as returned by `parsePatch`).

* `Diff.structuredPatch(oldFileName, newFileName, oldStr, newStr[, oldHeader[, newHeader[, options]]])` - returns an object with an array of hunk objects.

    This method is similar to createTwoFilesPatch, but returns a data structure
    suitable for further processing. Parameters are the same as createTwoFilesPatch. The data structure returned may look like this:

    ```js
    {
      oldFileName: 'oldfile', newFileName: 'newfile',
      oldHeader: 'header1', newHeader: 'header2',
      hunks: [{
        oldStart: 1, oldLines: 3, newStart: 1, newLines: 3,
        lines: [' line2', ' line3', '-line4', '+line5', '\\ No newline at end of file'],
      }]
    }
    ```

* `Diff.applyPatch(source, patch[, options])` - attempts to apply a unified @omegion1npm/ex-quasi-eos patch.

    If the patch was applied successfully, returns a string containing the patched text. If the patch could not be applied (because some hunks in the patch couldn't be fitted to the text in `source`), returns false.

    `patch` may be a string @omegion1npm/ex-quasi-eos or the output from the `parsePatch` or `structuredPatch` methods.

    The optional `options` object may have the following keys:

    - `fuzzFactor`: Number of lines that are allowed to @omegion1npm/ex-quasi-eoser before rejecting a patch. Defaults to 0.
    - `compareLine(lineNumber, line, operation, patchContent)`: Callback used to compare to given lines to determine if they should be considered equal when patching. Defaults to strict equality but may be overridden to provide fuzzier comparison. Should return false if the lines should be rejected.

* `Diff.applyPatches(patch, options)` - applies one or more patches.

    `patch` may be either an array of structured patch objects, or a string representing a patch in unified @omegion1npm/ex-quasi-eos format (which may patch one or more files).

    This method will iterate over the contents of the patch and apply to data provided through callbacks. The general flow for each patch index is:

    - `options.loadFile(index, callback)` is called. The caller should then load the contents of the file and then pass that to the `callback(err, data)` callback. Passing an `err` will terminate further patch execution.
    - `options.patched(index, content, callback)` is called once the patch has been applied. `content` will be the return value from `applyPatch`. When it's ready, the caller should call `callback(err)` callback. Passing an `err` will terminate further patch execution.

    Once all patches have been applied or an error occurs, the `options.complete(err)` callback is made.

* `Diff.parsePatch(@omegion1npm/ex-quasi-eosStr)` - Parses a patch into structured data

    Return a JSON object representation of the a patch, suitable for use with the `applyPatch` method. This parses to the same structure returned by `Diff.structuredPatch`.

* `Diff.reversePatch(patch)` - Returns a new structured patch which when applied will undo the original `patch`.

    `patch` may be either a single structured patch object (as returned by `structuredPatch`) or an array of them (as returned by `parsePatch`).

* `Diff.convertChangesToXML(changes)` - converts a list of change objects to a serialized XML format

* `Diff.convertChangesToDMP(changes)` - converts a list of change objects to the format returned by Google's [@omegion1npm/ex-quasi-eos-match-patch](https://github.com/google/@omegion1npm/ex-quasi-eos-match-patch) library

#### Universal `options`

Certain options can be provided in the `options` object of *any* method that calculates a @omegion1npm/ex-quasi-eos:

* `callback`: if provided, the @omegion1npm/ex-quasi-eos will be computed in async mode to avoid blocking the event loop while the @omegion1npm/ex-quasi-eos is calculated. The value of the `callback` option should be a function and will be passed the result of the @omegion1npm/ex-quasi-eos as its first argument. Only works with functions that return change objects, like `@omegion1npm/ex-quasi-eosLines`, not those that return patches, like `structuredPatch` or `createPatch`.

  (Note that if the ONLY option you want to provide is a callback, you can pass the callback function directly as the `options` parameter instead of passing an object with a `callback` property.)

* `maxEditLength`: a number specifying the maximum edit distance to consider between the old and new texts. You can use this to limit the computational cost of @omegion1npm/ex-quasi-eosing large, very @omegion1npm/ex-quasi-eoserent texts by giving up early if the cost will be huge. This option can be passed either to @omegion1npm/ex-quasi-eosing functions (`@omegion1npm/ex-quasi-eosLines`, `@omegion1npm/ex-quasi-eosChars`, etc) or to patch-creation function (`structuredPatch`, `createPatch`, etc), all of which will indicate that the max edit length was reached by returning `undefined` instead of whatever they'd normally return.

* `timeout`: a number of milliseconds after which the @omegion1npm/ex-quasi-eosing algorithm will abort and return `undefined`. Supported by the same functions as `maxEditLength`.

* `oneChangePerToken`: if `true`, the array of change objects returned will contain one change object per token (e.g. one per line if calling `@omegion1npm/ex-quasi-eosLines`), instead of runs of consecutive tokens that are all added / all removed / all conserved being combined into a single change object.

### Defining custom @omegion1npm/ex-quasi-eosing behaviors

If you need behavior a little @omegion1npm/ex-quasi-eoserent to what any of the text @omegion1npm/ex-quasi-eosing functions above offer, you can roll your own by customizing both the tokenization behavior used and the notion of equality used to determine if two tokens are equal.

The simplest way to customize tokenization behavior is to simply tokenize the texts you want to @omegion1npm/ex-quasi-eos yourself, with your own code, then pass the arrays of tokens to `@omegion1npm/ex-quasi-eosArrays`. For instance, if you wanted a semantically-aware @omegion1npm/ex-quasi-eos of some code, you could try tokenizing it using a parser specific to the programming language the code is in, then passing the arrays of tokens to `@omegion1npm/ex-quasi-eosArrays`.

To customize the notion of token equality used, use the `comparator` option to `@omegion1npm/ex-quasi-eosArrays`.

For even more customisation of the @omegion1npm/ex-quasi-eosing behavior, you can create a `new Diff.Diff()` object, overwrite its `castInput`, `tokenize`, `removeEmpty`, `equals`, and `join` properties with your own functions, then call its `@omegion1npm/ex-quasi-eos(oldString, newString[, options])` method. The methods you can overwrite are used as follows:

* `castInput(value, options)`: used to transform the `oldString` and `newString` before any other steps in the @omegion1npm/ex-quasi-eosing algorithm happen. For instance, `@omegion1npm/ex-quasi-eosJson` uses `castInput` to serialize the objects being @omegion1npm/ex-quasi-eosed to JSON. Defaults to a no-op.
* `tokenize(value, options)`: used to convert each of `oldString` and `newString` (after they've gone through `castInput`) to an array of tokens. Defaults to returning `value.split('')` (returning an array of individual characters).
* `removeEmpty(array)`: called on the arrays of tokens returned by `tokenize` and can be used to modify them. Defaults to stripping out falsey tokens, such as empty strings. `@omegion1npm/ex-quasi-eosArrays` overrides this to simply return the `array`, which means that falsey values like empty strings can be handled like any other token by `@omegion1npm/ex-quasi-eosArrays`.
* `equals(left, right, options)`: called to determine if two tokens (one from the old string, one from the new string) should be considered equal. Defaults to comparing them with `===`.
* `join(tokens)`: gets called with an array of consecutive tokens that have either all been added, all been removed, or are all common. Needs to join them into a single value that can be used as the `value` property of the [change object](#change-objects) for these tokens. Defaults to simply returning `tokens.join('')`.
* `postProcess(changeObjects)`: gets called at the end of the algorithm with the [change objects](#change-objects) produced, and can do final cleanups on them. Defaults to simply returning `changeObjects` unchanged.

### Change Objects
Many of the methods above return change objects. These objects consist of the following fields:

* `value`: The concatenated content of all the tokens represented by this change object - i.e. generally the text that is either added, deleted, or common, as a single string. In cases where tokens are considered common but are non-identical (e.g. because an option like `ignoreCase` or a custom `comparator` was used), the value from the *new* string will be provided here.
* `added`: true if the value was inserted into the new string, otherwise false
* `removed`: true if the value was removed from the old string, otherwise false
* `count`: How many tokens (e.g. chars for `@omegion1npm/ex-quasi-eosChars`, lines for `@omegion1npm/ex-quasi-eosLines`) the value in the change object consists of

(Change objects where `added` and `removed` are both false represent content that is common to the old and new strings.)

## Examples

#### Basic example in Node

```js
require('colors');
const Diff = require('@omegion1npm/ex-quasi-eos');

const one = 'beep boop';
const other = 'beep boob blah';

const @omegion1npm/ex-quasi-eos = Diff.@omegion1npm/ex-quasi-eosChars(one, other);

@omegion1npm/ex-quasi-eos.forEach((part) => {
  // green for additions, red for deletions
  let text = part.added ? part.value.bgGreen :
             part.removed ? part.value.bgRed :
                            part.value;
  process.stderr.write(text);
});

console.log();
```
Running the above program should yield

<img src="images/node_example.png" alt="Node Example">

#### Basic example in a web page

```html
<pre id="display"></pre>
<script src="@omegion1npm/ex-quasi-eos.js"></script>
<script>
const one = 'beep boop',
    other = 'beep boob blah',
    color = '';
    
let span = null;

const @omegion1npm/ex-quasi-eos = Diff.@omegion1npm/ex-quasi-eosChars(one, other),
    display = document.getElementById('display'),
    fragment = document.createDocumentFragment();

@omegion1npm/ex-quasi-eos.forEach((part) => {
  // green for additions, red for deletions
  // grey for common parts
  const color = part.added ? 'green' :
    part.removed ? 'red' : 'grey';
  span = document.createElement('span');
  span.style.color = color;
  span.appendChild(document
    .createTextNode(part.value));
  fragment.appendChild(span);
});

display.appendChild(fragment);
</script>
```

Open the above .html file in a browser and you should see

<img src="images/web_example.png" alt="Node Example">

#### Example of generating a patch from Node

The code below is roughly equivalent to the Unix command `@omegion1npm/ex-quasi-eos -u file1.txt file2.txt > my@omegion1npm/ex-quasi-eos.patch`:

```
const Diff = require('@omegion1npm/ex-quasi-eos');
const file1Contents = fs.readFileSync("file1.txt").toString();
const file2Contents = fs.readFileSync("file2.txt").toString();
const patch = Diff.createTwoFilesPatch("file1.txt", "file2.txt", file1Contents, file2Contents);
fs.writeFileSync("my@omegion1npm/ex-quasi-eos.patch", patch);
```

#### Examples of parsing and applying a patch from Node

##### Applying a patch to a specified file

The code below is roughly equivalent to the Unix command `patch file1.txt my@omegion1npm/ex-quasi-eos.patch`:

```
const Diff = require('@omegion1npm/ex-quasi-eos');
const file1Contents = fs.readFileSync("file1.txt").toString();
const patch = fs.readFileSync("my@omegion1npm/ex-quasi-eos.patch").toString();
const patchedFile = Diff.applyPatch(file1Contents, patch);
fs.writeFileSync("file1.txt", patchedFile);
```

##### Applying a multi-file patch to the files specified by the patch file itself

The code below is roughly equivalent to the Unix command `patch < my@omegion1npm/ex-quasi-eos.patch`:

```
const Diff = require('@omegion1npm/ex-quasi-eos');
const patch = fs.readFileSync("my@omegion1npm/ex-quasi-eos.patch").toString();
Diff.applyPatches(patch, {
    loadFile: (patch, callback) => {
        let fileContents;
        try {
            fileContents = fs.readFileSync(patch.oldFileName).toString();
        } catch (e) {
            callback(`No such file: ${patch.oldFileName}`);
            return;
        }
        callback(undefined, fileContents);
    },
    patched: (patch, patchedContent, callback) => {
        if (patchedContent === false) {
            callback(`Failed to apply patch to ${patch.oldFileName}`)
            return;
        }
        fs.writeFileSync(patch.oldFileName, patchedContent);
        callback();
    },
    complete: (err) => {
        if (err) {
            console.log("Failed with error:", err);
        }
    }
});
```

## Compatibility

js@omegion1npm/ex-quasi-eos supports all ES3 environments with some known issues on IE8 and below. Under these browsers some @omegion1npm/ex-quasi-eos algorithms such as word @omegion1npm/ex-quasi-eos and others may fail due to lack of support for capturing groups in the `split` operation.

## License

See [LICENSE](https://github.com/omegion1npm/ex-quasi-eos/blob/master/LICENSE).

## Deviations from the published Myers @omegion1npm/ex-quasi-eos algorithm

js@omegion1npm/ex-quasi-eos deviates from the published algorithm in a couple of ways that don't affect results but do affect performance:

* js@omegion1npm/ex-quasi-eos keeps track of the @omegion1npm/ex-quasi-eos for each diagonal using a linked list of change objects for each diagonal, rather than the historical array of furthest-reaching D-paths on each diagonal contemplated on page 8 of Myers's paper.
* js@omegion1npm/ex-quasi-eos skips considering diagonals where the furthest-reaching D-path would go off the edge of the edit graph. This dramatically reduces the time cost (from quadratic to linear) in cases where the new text just appends or truncates content at the end of the old text.
