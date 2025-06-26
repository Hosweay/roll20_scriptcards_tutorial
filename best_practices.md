# ScriptCards Best Practices

This will be a collection of what I think are some best practices to follow to make your life as a writer of ScriptCards easier. Some of these may be similar to general software development best practices but most are ScriptCards specific. As always, this is just like my opinion, so feel free to ignore any or all of these if you feel like it. Which brings me to the first best practice:

## If it works how you and your players want, that's it

That's the number one best practice. Almost no one but you will ever read your ScriptCard code, so however you get things working is great. You and your players having fun is way more important than having optimal and impressive ScriptCards in your game.

## Start Small

I certainly have a tendency, when I get an idea for a new ScriptCard, to dive right in and start writing it immediately. And for small ScriptCards this is fine. However, when you have a large or complicated set of automations in mind, it is often better to start small. Get one small piece working first. Once you have that section working, move on to another small section and keep building the ScriptCard up.

Another option that may work for you is to write out all of the steps and decisions you make manually during the game. Add [comments](https://wiki.roll20.net/Script:ScriptCards#Comment_.28--.2F.29) for each step and let the comments serve as your guide for everything you will eventually need to write. You might have a start that looks like this:

```text
--/|Player chooses a target
--/|Player chooses which weapon to attack with
--/|Determine any status effects that will modify the attack
--/|Make attack roll
--/|Compare attack roll to defensive stat of target
--/|If attack roll is higher than the defensive stat, roll damage
--/|apply any damage modifiers to the damage roll
--/|apply damage to the target
```

With that outline in place you can now see sections that you can work on piece by piece until you get everything working how you want.

## Seldom Repeat Yourself

In software development, you may hear the term DRY, standing for Don't Repeat Yourself. Well ScriptCards aren't actually software and most people that write them are not developers, so you don't need to go to extreme lengths to make sure you never have repeated code.

That said, if you find yourself doing the same things over and over again in your code, examine if you can simplify it by turning those repeated sections into a function you can call. Look at the following:

```text
!script {{
    --&Feature1|feature1
    --&Feature2|feature2
    --&Feature3|feature3

    --:CheckFeature1|
    --Rfind|@{selected|character_id};[&Feature1];repeating_feature;name
    --&Feature1Property|[*R:property]

    --:CheckFeature2|
    --Rfind|@{selected|character_id};[&Feature1];repeating_feature;name
    --&Feature2Property|[*R:property]

    --:CheckFeature3|
    --Rfind|@{selected|character_id};[&Feature1];repeating_feature;name
    --&Feature3Property|[*R:property]
}}
```

The above ScriptCard would work, assuming you have a character sheet with a repeating section named repeating_feature and you now have variables for those property values. Great. But imagine if you want to now set another variable for each feature. Well in the above code, you would have to add that to each of the CheckFeatureX sections. Compare that with the following:

```text
!script {{
    --&CharID|@{selected|character_id}

    -->CheckFeature|[&CharID];[&Feature1];Feature1Property
    -->CheckFeature|[&CharID];[&Feature2];Feature2Property
    -->CheckFeature|[&CharID];[&Feature3];Feature3Property

    --X|
    --:CheckFeature|CharacterID;FeatureToFind;StringVariableToReturn
        --Rfind|[%1%];[%2%];repeating_feature;name
        --&[%3%]|[*R:property]
    --<|
}}
```

With the above code, you can make any changes you need, whether they are bug fixes or new features, in one place and the caller does not necessarily need to change.

## Put Configuration Near the Top

Putting configuration variables near the top will save you from having to dig through large ScriptCards if you want to make changes or customize them.

## Brevity is the Soul of Future Frustrations

Give your variables descriptive names. In a small ScriptCard with only 1 roll variable, a variable named `--=Roll|1d6` is probably fine but in a large ScriptCard with many rolls and variables, having variables named `[$Roll1]`, `[$Roll2]`, `[$Roll3]`, `[&a]`, `[&b]`, and `[&k]`, you will find yourself scrolling up and back down a lot to find out what in the hell those variables are referring.

Give future you a little bit of an advantage give your variables a descriptive name that you can tell what that is at a glance. `[&TokenID]` is quite a bit clearer even without additional context than `[&t]`. `[$AttackRoll]` and `[$DamageRoll]` are pretty clear without additional context as well.

Saving the 2 seconds it will take to type out a descriptive variable name will be worth minutes or hours for future you having to scan to find the context for what your 1 letter variable contains.

## Keep Your Conditions as Simple as Possible

[Conditions](https://wiki.roll20.net/Script:ScriptCards#Conditional_Statement_.28--.3F.29) are extremely powerful and allow ScriptCards to programmatically determine the right path through complex possibilities. Many of your ScriptCards are going to contain at least one and possibly many conditionals. However keep your conditions as simple as possible because while `--?[$AttackRoll] -ge [*T:defense]|` or even `--?[$AttackRoll] -ge [*T:defense] -and "[*T:shield]" -ne "up"|` might be easy enough to follow, adding more conditions does not increase complexity linearly. The combinations get exponentially harder to reason about the more you add. For example:

```text
--?[$AttackRoll] -ge [*T:defense] -and "[*T:shield]" -ne "up" -or "[*T:visibility]" -eq "cloaked" -and "[*S:capabilities]" -ninc "anti-cloaking"|
```

The above example is a lot more complicated than a single or even a two part conditional. The ScriptCards wiki contains more information on how [multi-part conditionals](https://wiki.roll20.net/Script:ScriptCards#Multi-part_Conditionals) work in ScriptCards.

This recommendation is to break up your complex conditions in smaller conditions that are much clearer on if they will evaluate to True or to False at a glance. If when you are writing a conditional and you need more than 3 parts, pause and ask yourself if you can split this up and make decisions at separate locations rather than all at the same time in a single statement.

## Add Double Quotes to Your String Variables in Conditions

In many ScriptCards, you will see example conditions of `--?[$AttackRoll] -eq 20|`. And for numeric checks, not having quotes should not impact you. But when you are using a string variable in your conditional, it's best to add double quotes. For example:

```text
--Rfirst|@{selected|character_id};repeating_weapons
--:WeaponLoop
--?[*R:name] -inc "Longbow"|+Bow Name;[*R:name]
--Rnext|
--?[*R:name] -ne "NoRepeatingAttributeFound"|WeaponLoop
```

The above snippet may work fine until you give your player a Longbow of Total Awesomeness and then those spaces cause all sorts of havoc and you end up with bad output and a nasty message in the Mod Console and a look of disapproval from Kurt on the ScriptCards Discord when you ask about it.

So in the above example just add double quotes so things look like `--?"[*R:name]"`. So when you are using a string variable or reference lookup in a condition, add double quotes. It will save you from unexpected issues when the string you are checking has spaces or potenially a single-quote or apostrophe.

## Minimize the Roll20isms in your ScriptCards

What is a Roll20ism? In this I mean, the number of [attribute calls](https://help.roll20.net/hc/en-us/articles/360039675133-Journal#Journal-Attributes&AbilitiesTab) `@{}`, [ability calls](https://help.roll20.net/hc/en-us/articles/360039675133-Journal#Journal-CharacterSheetTab) `%{}`, and [roll queries](https://wiki.roll20.net/Roll_Query) `?{}` in your ScriptCards. I know some people will not agree with this suggestion and that's understandable. I mean a good `@{selected|token_id}` is handy and I use that in many of my ScriptCards. People also really like the drop down of a roll query to get information and that can make sense.

The reason I suggest to minimize the usage of these Roll20 features is precisely because they are Roll20 features. These features get processed before ScriptCards even gets information. Your ScriptCard may look like:

```text
!script {{
    --#title|@{selected|token_name}
    --+Choice|?{Choice|Attack|Parry|Riposte}
}}
```

But by the time ScriptCards is sent the text, it looks like this:

```text
!script {{
    --#title|Duel Master
    --+Choice|Riposte
}}
```

Which can be fine for gathering information at the start of a ScriptCard but it does come with some drawbacks. The main drawback is that you cannot conditionally execute them. Take the following example:

```text
!script {{
    --#sourceToken|@{selected|token_id}
    --?[*S:hp] -lt 10|[
        --&action|?{Injury Action|Drink Healing Potion|Dodge|Run away!}
    --]|
}}
```

That ScriptCard will not function how it appears to be written. The roll query will ALWAYS run, regardless of the hp attribute.

Similar things can show up with attribute calls like in the following example:

```text
!script {{
--?@{selected|hp} -lt 20|[
    --:Regenerate|
    --!a:@{selected|character_id}|hp:+=9
--]|

--?@{selected|hp} -gt 20|[
    --+Recovered|Your regeneration abilties have replenished you and you are no longer hindered.
--]|
}}
```

The above ScriptCard will never print the Recovered output regardless if the 9 hp move the hp over 20. Remember that all attribute calls are processed first. Which means that if you run that ScriptCard and the selected character has an hp attribute of 15, ScriptCards will get sent the following:

```text
!script {{
    --?15 -lt 20[
        --:Regenerate|
        --!a:@{selected|character_id}|hp:+=9
    --]|

    --?15 -gt 20|[
        --+Recovered|Your regeneration abilties have replenished you and you are no longer hindered.
    --]|
}}
```

So you can see that even though the regeneration would put the hp attribute from 15 to 24, the second conditional will not recognize that. This is why I recommend using [ScriptCard attribute references](https://wiki.roll20.net/Script:ScriptCards#Object_Attribute_Referencing) like `[*S:hp]` and `[*T:npc_ac]`. Using those Roll20isms selectively will end up saving you a lot of confusion in the long run when things aren't working how you expect.

## Use String Variables for All Non-Dice Rolls

Listen, we all like rolling dice. Random chance is at the core of many of the games we play. For those cases when you need a d20 or 2d6 or now with Daggerheart a couple d12s, by all means, the [roll variable](https://wiki.roll20.net/Script:ScriptCards#Roll_Variable_Assignment_.28--.3D.29) is perfect for that. Use it. Rolls are great.

Now with that preamble out of the way, not every numeric value needs to be in a roll variable. [String variables](https://wiki.roll20.net/Script:ScriptCards#String_Variable_Assignment_.28--.26.29) can store numeric values in them. String variables can even be used in numeric comparisons in a conditional.

```text
--&NumberOfAttacks|3
--?[&NumberOfAttacks] -gt 1|AttackLoop
```

That example works. There is no special property that a roll variable would bring to a static number value like that.

Ok great, you might be saying, string variables _can_ work but why are you saying it's a best practice? Fair question. There are multiple reasons. The first is a common mistake we all have experienced when writing ScriptCards, let's take a look at the following snippet:

```text
--=AttackRoll|1d20 + [*S:attack_mod]
--?[$AttackRoll] -gt [*T:defensive_mod]|Hit|Miss

--:Hit|
    --=DamageRoll|1d8 + [*S:damage_mod]
    --+HIT|[*S:t-name] rolls a [=AttackRoll] and hits [*T:t-name] for [=DamageRoll]
--^Done|
--:Miss|
    --+MISS|[*S:t-name] swings wildly and misses rolling a [=AttackRoll]
--:Done|
--X|
```

Now there will be some of you that may immediately spot the problem with the above ScriptCard and in this small snippet it may be obvious but roll variables use two different symbols, the equals sign `=` when declaring the roll variable and the dollar sign `$` when calling the variable. It's easy to mess those up and the more you use them for, the increased likelihood you will make a mistake. String variables have an advantage of using the same symbol for both declaration and usage, the ampersand `&`.

Also, you might not be aware that you can use string variables even when you need to do a little math. You are probably aware you can use string variables in roll variable formulas `--=Roll|1d8 + [&Modifiers]` but you may not be aware of the inline roll function of ScriptCards `--&counter|[= [&counter] + 1]`. So in that bit, technically you would be correct if you said there is a roll variable there. ScriptCards creates an unnamed roll variable when it sees `[=]` in use. But you don't need to interact with that roll variable, you can keep using your string variable for counter in all the places you need.

## Avoid Code Blocks in Libraries

[Code blocks](https://wiki.roll20.net/Script:ScriptCards#Code_Blocks) can be extremely handy. I use code blocks in my ScriptCards quite often. Similarly, libraries are extremely handy for sharing and reusing common functions. Both of these features are great and please use both of them; just do not use code blocks in your libraries.

Why you might ask. It is because, at least as I am writing this, ScriptCards' code blocks cannot be nested, which means you cannot have a code block inside another code block. So the following ScriptCard will probably not function as you want:

```text
!script {{
    --&CoolFeature|true
    --&BasicFeature|false

    --?"[&BasicFeature]" -eq "true"|[
        --/|do stuff if true
    --]|[
        --?"[&CoolFeature]" -eq "true"|[
            --/|do stuff if basic feature is false and cool feature is true
        --]|
    --]|
}}
```

So in this ScriptCard, it is easy to see the nested code block, but what happens when you want put a function into a library that contains a code block. You can probably imagine that eventually, you will probably forget your library's function contains a code block and you call that function from inside a code block.

```text
+++MyCustomLib+++

--?"[&BasicFeature]" -eq "true"|[
    --/|do stuff if true
--]|[
    -->CoolLibraryFeature|[&CoolFeature];FeatureReturn
--]|
```

The above code snippet looks fine on its own. There is nothing wrong with any of that as it stands. But imagine if your CoolLibraryFeature function in your MyCustomLib library looks like:

```text
--:CoolLibraryFeature|FeatureFlag;StringVariableToReturn
    --?"[%1%]" -eq "true"|[
        --&[%2%]|coolresult
    --]|[
        --&[%2%]|totallyuncoolman
    --]|
--<|
```
