Notes on Python scripting in Blender
====================================

Videos:

* https://youtu.be/XqX5wh4YeRw
* https://youtu.be/cyt0O7saU4Q
* https://youtu.be/rHzf3Dku_cE

The first has the most detail, the second and third are very light - the second one describes adding a new panel and the third is super light (it describes adding large numbers of monkey heads at random locations).

---

There's a _Scripting_ workspace - so add it (if it's not already present) and switch to it.

In _Preferences / Interface_, tick _Python Tooltips_ - now, if you hover over anything, you'll see how to refer to it in Python.

In the _Text_ editor, go to _Templates / Python / Ui Panel_ and click _Run_.

You can find all these template here - <https://github.com/blender/blender/tree/master/release/scripts/templates_py>

If you now go to the _Scene_ properties, you'll see a new expanded area called _Layout Demo_.

---

For my resulting first Python add-on for Blender see my [addon-world-to-basis](https://github.com/george-hawkins/addon-world-to-basis) repo.
