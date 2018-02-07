# Shortcut for console.log in Spacemacs

## Introduction

Against all best practices, I debug js code using `console.log`. I also use
Spacemacs, which I [wrote about before](). Typing this code over and over again
became tedious, so I wanted a way to make it faster. It was also one of the
first things I ever wrote in elisp, so here's what I learned.

## Defining a new binding

Because I only wanted this key binding to apply when using `rjsx-mode`, I used
the function `spacemacs/set-leader-keys-for-major-mode`. This established a new
key binding behind the leader key, so it can be accessed like all other Spacemacs
mode bindings (through `SPC m` or `,`).

The code for my binding ended up looking like this:

```elisp
(spacemacs/set-leader-keys-for-major-mode 'rjsx-mode "l" 'insert-console-log)
```

`'insert-console-log` is the symbol for a function that I defined separately. I
found this useful because then I could reevaluate the function definition. Since
the keybinding was still set up, I could then test it without needing to close
any files or restart emacs.

## Function definition

This is what my `insert-console-log` function looks like.

```elisp
  (defun insert-console-log ()
    "Insert console.log of the variable at point."
    (interactive)
    (let ((beg nil)
          (end nil))
      (back-to-indentation)
      (setq beg (point))
      (end-of-line)
      (setq end (point))
      (kill-region beg end)
      (insert (format "console.log(\"%s\", %s)"
                      (car kill-ring)
                      (car kill-ring)))))
```

This function first initializes two variables, `beg` and `end`. After that it
moves the cursor to the start of the current line (minus indentation) with
`back-to-indentation`, and sets `beg` to the cursor position there. After that
it moves to the end of the line and sets `end` to the cursor position there.
`kill-region` copies the text between those two positions to the kill ring and
deletes it from the buffer. The final insert adds a `console.log` statement to
the buffer, interpolating the first item from the kill ring twice ( with `( car
kill-ring )`).

## Credit where credit is due

I had tried to dig into elisp and Spacemacs code a few times before and had
always found it challenging. On [one of my favorite
podcasts](https://theartofcharm.com/podcast/) the host often talks about how
paying for coaching to help you improve at skills is always a great investment.

[Codementor](https://www.codementor.io/) is a platform that allows you to hire
programmers to coach you in specific areas. Most of what I learned for this key
binding came from pairing with [Rudolf
Olah](https://www.codementor.io/rudolfolah), who was very patient when I got
frustrated during the learning. Thanks Rudolf!
