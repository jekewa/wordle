# Wordle

This is a simple bash script to help reduce possible solutions for the [New York Times Wordle](https://www.nytimes.com/games/wordle/index.html) game.

Wordle is a straight-forward guessing game, where you try to work out the 5-letter word of the day within six guesses.
After each guess, the game will shade the letters to let you know which you got in the right place, which are in the wrong place, or which aren't in the solution at all.
To help, it shows a keyboard with the letters also shaded to let you know what's found or eliminated.
Leveraging that information, you can make another guess, repeating until you run out or solve the problem.

It's a clever, and sometimes frustrating, experiment in recalling words, or working out words you don't know, from the clues as you guess.
It's mystifying how quickly it works one into a brain fog, or makes you dig words from deep memory, or sometimes introducing "letter patterns" into words you didn't know.

Then the game lets you copy your guess grid, colors only, to share with your friends on whatever social network you like, whether they want to see it or not.  

## WordleBot

[WordleBot](https://www.nytimes.com/interactive/2022/upshot/wordle-bot.html) is sometimes a jerk.
It's been updated to be a little more positive, but especially if you miss a bit, its analysis comes off a little superior, which can be hard to take from an analytics script.

Plus it cheats.
It uses the possible solutions to help it make smarter chocies.
If you're like a normal human, you don't know the list of solutions and try to play from your pool of 5-letter words you know or guesses you make.

But, WordleBot does give a good analysis of your play, and if you don't give too much credit to its "smarts," it can help make you a better player.

Alas, it's also behind a pay wall.
You can subscribe, or use different browsers, or hack the HTML to disable the masking divs...
If you don't do that, you can only use it a few times before your article limit runs out.

## Unofficial Archive

One purposeful draw, but frustrating thing about Wordle is it only lets you play one game a day.
This keeps it from occupying all of our time, I guess.

There's a site, [Taq Karim's Unofficial Wordle archive](https://mottaquikarim.github.io/wordle_timemachine/) where the "settings" allows you to pick a date in Wordle's past to play.
On completing a game, the end display lets you play a different game, a day before or after.

It's a fun way to go back and make up for the hundreds of games you might have missed.

## Wordle Solutions

There are many articles out there that explain how you can find the wordle solutions from the JavaScript in the app.
For example, [Better Programming](https://betterprogramming.pub/how-to-beat-wordle-every-day-an-easy-cheat-3c1dc51bc4fc) shows a simple console bit you can call to run to get today's answer.

Exploring the JavaScript also shows the array of answers, and an array of other acceptable 5-letter words.
Wordle doesn't allow use all of the 5-letter English words, like if you compared it to the Linux /usr/dict/words file.
This solution file is at least obscured a little, where the words aren't listed in game-play order.
The JavaScript does reveal the date-based math to find the index of today's word, which is how that other script works.

# Wordle Help

This repo contains a simple bash script and a file with all the solutions taken from the Worlde JavaScript.
The bash script allows following along with Wordle game play to share a list of words from the solution that match what is learned after each guess.

This is intended to help guide through the subset of offerings, more than really steer to a best-choice solution.
It does give an edge because it will eliminiate unacceptable solutions.
It also kicks out the brain-fog that comes with the incorrect indexing brains have when trying to think of all of the 5-letter words that contain this set, but not that set, with these in place, and those in the wrong places...

## Help

Typing `./wordle -h` will display the simple help text:

```
Usage: ./wordle -a (ANY) -e (EXACT) -i IGNORE -m MISPLACED -h
 -a ANY:        Characters found in wrong position (yellow in Wordle), ensuring they are in the solutions
 -e EXACT:      Characters found in correct position, using periods for yet unknowns (green in Wordle), ensuring solution matches
                  Format such as '..e..' when only E is known to be in the center
 -i IGNORE:     Charcters found not in word (grey in Wordle), keeping them out of the solutions
 -m MISPLACED:  Characters found in wrong  position, but using periods for other characters (yellow in Wordle), filtering them from solutions
                  Format such as '..e..' when E is known to be not in the center

Example: ./wordle -a abc -e ....e -i fgh
  This will search for words ending in 'e' that contain 'a' and 'b' and 'c' that don't have 'f' or 'g' or 'h'
```

The help text tries to give a good reason for each of the parameters.
It's flexible and allows the parameters in any order.
The parameters can be repeated, so you can add them after each play.

The -e is the only "inflexible" parameter.
It can be repeated in the command line, but only one "exact" works, keeping the last found.

## Example

Let's not know the word of the day is SLACK, and see how the script can help us get there.
Pretend we start with the WordleBot blessed starting word LEAST.
Any word with those letters gets kudos from WordleBot.

Wordle will tell us that E and T aren't in the solution, making them dark grey.
It will also tell us that L and S are, but are in the wrong spot, making them yellow.
And we'll see that the letter A is in the correct place, making it green.

We can get the filtered list from the wordle script using the following example, adding those parameters as outlined above:

`./wordle -i et -a las -e ..a..`

Note, while the discussion (and Wordle) uses capital letters to call them out in the text, the solutions file and parameters need to be lowercase.

That parameter list says "ignore E and T, include anywhere L and A and S, and exactly match A in the center of a 5-letter word."
Because of the exact match, you can leave the A out of the "any" parameter, but it doesn't hurt, and can help if that letter appears in more than one place.

Which gives this resulting list:

```
shall
shawl
glass
class
scald
snarl
slack
snail
clasp
psalm
slang
scalp
scaly
slash
flash
clash
slain
small
flask
```

Optionally, we can add the parameter to filter the misplaced letters.
This will remove words with those letters in the wrong spot.
Because of the way the regular expressions work, we want to call out each separately.

`./wordle -i et -a las -e ..a.. -m l.... -m ...s.`

Which gives this slightly shorter list: 

```
shall
shawl
scald
snarl
slack
snail
psalm
slang
scalp
scaly
slain
small
```

Note the first list didn't have any words that started with an L, so there isn't a lot of value in that.
We might not know that, though, if we hadn't done the first call.
Clearly, the second list removed any words where S was next-to-last, such as SLASH and CLASP.

Imagine next we try the word SHALL, just because it's first, not because it's a better choice than others.
We learn that the S is in the right place, as it turns green. 
We learn that H is not included, so it turns grey.
The letter A is still in the right spot, so it stays green.
It should be that one L is yellow and one is grey, as we know our solution only needs one, but it oculd be both are yellow because neither is in the right place.

We can modify our command to filter more, by either augmenting the parameters in place, or adding new ones to the end:

`./wordle -i et -a las -e ..a.. -m l.... -m ...s. -i h -m ...l. -m ....l -e s.a..`

Note the added -i for the ignored H, and the added -m for the filtering positions of the known bad Ls.
The -m for the Ls is repeated, or the list would only filter words with double-L at the end, and not PSALM or SNAIL in the example.
This should remove the word SHALL for certain, as well as any other words containing an H or with L in one of those other positions.

Also, adding the -e at the end will override the previous -e.
This is done so this simple "up arrow and edit" can make for a progressive filtering.

Alternatively, we can rewrite the parameter lists, with or without the known misplaced values:

```
./wordle -i eth -a las -e s.a..
./wordle -i eth -a las -e s.a.. -m l.... -m ...s. -m ...l. -m ....l
```

Without the misplaced parameters, we'd be left to self-filter the list as we looked at the game board to see which letters we shouldn't include.
Including the misplaced parameters does that filter on the list for us.
The result of this new parameter list  brings us to three possibilities!

```
slack
slang
slain
```

Certainly, with this knowledge, we're guaranteed to find a solution as we've only used two turns and have three possible solutions left.

We might  choose any of the words, and add another layer of parameters to our filter if we're wrong.
This gives us only one other positioned piece, the SLA start of the word.
Reasonably, choosing SLANG or SLAIN would allow us to leverage the N to know if it was the right or other word, or neither, so those could be smart choices.
If we did chose SLANG or SLAIN, we'd see that only SLACK remains, so the worst case for this example is a 4-turn guess.

That's where the game has its charm.

Not all games pare down this quickly or so close to guarantee a solution in six turns.

## The Innards

The script itself is pretty simple.

It parses the parameters and builds the potentially growing lists.

For the -i and -e paramters, it's simple regular expression matching to ensure that the string either matches or doesn't contain the values, as appropriate.
The -m parameters combine in a big "or" regular expression to make sure those matches are eliminated.
The -a parameters are combined into a little bit of a harder "or" that uses lookaheads to ensure that all of those letters are included anywhere in the possible solutions.

The script brute forces through each of the possible solutions and compares against the regular expressions

### ggrep

Note that I wrote this on my Mac, and the MacOS grep command doesn't support lookaheads, so I had to use the ggrep version (which I added through Homebrew). 
As such, the -a parameter won't work with the script as written not on a Mac, or on a Mac without ggrep added.

I might change this to be a little more savvy in the future, but for now, it suits my needs and can still function as an example for anyone else.

