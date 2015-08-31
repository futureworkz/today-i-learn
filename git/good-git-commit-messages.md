# Good Git Commit Messages

There are actually two parts to a git commit message:
1) A single one line commit message
2) A long description

When we do `git commit` (without the `-m "message"`), git will automatically put us in our editor.

The first line of the editor will the single one line commit message.

Leave a blank line after the first and then on the third line, write the long description.

The description should give the context of the commit - why this code is made, what problem it address, what feature is completed, etc.

The long description gives contextual information about the commit. This makes thing easier when we are reviewing the code in the future. The downside? Just 5-10 minutes of your time.

For more information, you can read https://robots.thoughtbot.com/5-useful-tips-for-a-better-commit-message
