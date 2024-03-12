# ScriptCards Tutorial

This is intended to be a tutorial for Roll20 Pro subscribers looking to automate aspects of their games with the very versatile ScriptCards Mod

## Step 1: Installation

The easiest way to install ScriptCards is via the 1Click Mod installation method.

[Roll20 Mod installation Documentation](https://help.roll20.net/hc/en-us/articles/360037256714-Roll20-Mods-API#API-HowdoIinstallanAPIscriptusingtheone-clickscriptlibrary?)
[Nick Olivo YouTube video showing API installation](https://www.youtube.com/watch?v=jam2yx8btaQ)

## Step 2: Check ScriptCards is running

In the Roll20 chat, type the following `!script {{ }}`

![Screenshot of step showing ScriptCards installation test](images/tutorial1/step2_example.png)

#### Step 2 Explanation

Roll20 API Mods like ScriptCards are marked by an exclamation point. ScriptCards will respond to !script and !scriptcard. They are equivalent and tutorials might use either interchangably.

ScriptCards code goes between pairs of double curly braces. Two opening curly braces starts the processing of a ScriptCard and two closing curly braces signals the end of the ScriptCard.

Seeing the output above will show that your game's API sandbox is running and ScriptCards Mod is installed and running.

## Step 3: Your First ScriptCard

### Let's Make an Attack

```text
!scriptcard {{
    --=AttackRoll|1d20
    --+Attack Roll Result|[$AttackRoll]
}}
```

![screenshot of Scriptcards rolling an attack and displaying the roll](images/tutorial1/step3-1_attackroll.png)

#### Step 3-1 Explanation

### Let's Add a Custom Title

```text
!scriptcard {{
    --#title|Attack Roll
    --=AttackRoll|1d20
    --+Attack Roll Result|[$AttackRoll]
}}
```

![screenshot of Scriptcards with a custom title](images/tutorial1/step3-2_attackroll_title.png)

#### Step 3-2 Explanation

### Let's Add a Description

```text
!scriptcard {{
    --#title|Attack Roll
    --#emoteText|Barbarian Attacks
    --=AttackRoll|1d20
    --+Attack Roll Result|[$AttackRoll]
}}
```

![screenshot of Scriptcards with a description via emoteText setting](images/tutorial1/step3-3_attackroll_desc.png)

#### Step 3-3 Explanation

### Let's Add the Token's Picture to the Description

```text
!scriptcard {{
    --#sourceToken|@{selected|token_id}
    --#title|Attack Roll
    --#emoteText|Barbarian Attacks
    --=AttackRoll|1d20
    --+Attack Roll Result|[$AttackRoll]
}}
```
![screenshot of the attacker token](images/tutorial1/attacker_token.png)
![screenshot of Scriptcards with the selected token](images/tutorial1/step3-4_attackroll_sourceToken.png)

Roll20 processes all @{} references before sending the results to ScriptCards.
If you see the following Roll20 errror, it often means you have @{selected} without having a token selected.

![screenshot of Roll20 error when no selected token](images/tutorial1/tutorial1_roll20_error_message.png)

#### Step 3-4 Explanation

### Let's Add the Attacker's Attributes

```text
!scriptcard {{
    --#sourceToken|@{selected|token_id}
    --#title|Attack Roll
    --#emoteText|Barbarian Attacks
    --=AttackRoll|1d20 + [*S:strength_mod] [STR] + [*S:pb] [PROF]
    --+Attack Roll Result|[$AttackRoll]
}}
```
![screenshot of Scriptcards output using the attacker attributes](images/tutorial1/step3-5_attackroll_strength_mod.png)

#### Step 3-5 Explanation

### Let's Add a Target for the Attack

```text
!scriptcard {{
    --#sourceToken|@{selected|token_id}
    --#targetToken|@{target|token_id}
    --#title|Attack Roll
    --#emoteText|Barbarian Attacks [*T:t-name]
    --=AttackRoll|1d20 + [*S:strength_mod] [STR] + [*S:pb] [PROF]
    --+Attack Roll Result|[$AttackRoll]
}}
```
Let's add a new token for our character to attack.
![screenshot of the target token](images/tutorial1/target_token.png)
![screenshot of attacker and target tokens](images/tutorial1/tutorial1_attacker_target.png)
![screenshot of Scriptcards output when selecting a target token](images/tutorial1/step3-6_attackroll_target.png)

#### Step 3-6 Explanation

### Let's Make It More Portable

```text
!scriptcard {{
    --#sourceToken|@{selected|token_id}
    --#targetToken|@{target|token_id}
    --#title|Attack Roll
    --#emoteText|[*S:character_name] Attacks [*T:t-name]
    --=AttackRoll|1d20 + [*S:strength_mod] [STR] + [*S:pb] [PROF]
    --+Attack Roll Result|[$AttackRoll]
}}
```
Let's add a new attacker token
![screenshot of new attacker token](images/tutorial1/tutorial1_new_attacker.png)
![screenshot of both attacker tokens and target token](images/tutorial1/tutorial1_all_tokens.png)
![screenshot of Scriptcards output with new attacker](images/tutorial1/step3-7_attackroll_nothardcoded1.png)
![screenshot of Scriptcards output with new attacker showing different modifiers in the roll](images/tutorial1/step3-7_attackroll_nothardcoded2.png)

#### Step 3-7 Explanation

### Let's Add the Target's Armor Class

```text
!scriptcard {{
    --#sourceToken|@{selected|token_id}
    --#targetToken|@{target|token_id}
    --#title|Attack Roll
    --#emoteText|[*S:character_name] Attacks [*T:t-name]
    --=AttackRoll|1d20 + [*S:strength_mod] [STR] + [*S:pb] [PROF]
    --+Attack Roll Result|[$AttackRoll] vs Armor Class [*T:t-bar2_value]
}}
```
![screenshot of Scriptcard output with target armor class](images/tutorial1/step3-8_attackroll_target_ac.png)

#### Step 3-8 Explanation

### Let's Make the AC Stand-out

```text
!scriptcard {{
    --#sourceToken|@{selected|token_id}
    --#targetToken|@{target|token_id}
    --#title|Attack Roll
    --#emoteText|[*S:character_name] Attacks [*T:t-name]
    --=AttackRoll|1d20 + [*S:strength_mod] [STR] + [*S:pb] [PROF]
    --+Attack Roll Result|[$AttackRoll] vs Armor Class [roll][*T:t-bar2_value][/roll]
}}
```
![screenshot of Scriptcards output when displaying target AC](images/tutorial1/step3-9_attackroll_ac_format.png)

#### Step 3-9 Explanation

### Let's See if the Attack Hits

```text
!scriptcard {{
    --#sourceToken|@{selected|token_id}
    --#targetToken|@{target|token_id}
    --#title|Attack Roll
    --#emoteText|[*S:character_name] Attacks [*T:t-name]
    --=AttackRoll|1d20 + [*S:strength_mod] [STR] + [*S:pb] [PROF]
    --+Attack Roll Result|[$AttackRoll] vs Armor Class [roll][*T:t-bar2_value][/roll]
    --?[$AttackRoll] -ge [*T:t-bar2_value]|Hit|Miss

    --:Done|
    --X|
    --:Hit|
        --+HIT|The [*S:character_name]'s attack hits [*T:t-name]
    --^Done|
    --:Miss|
        --+MISS|The [*S:character_name]'s attack misses [*T:t-name]
    --^Done|
}}
```
![screenshot of Scriptcards output checking hit or miss](images/tutorial1/step3-10_attackroll_hitmiss.png)

#### Step 3-10 Explanation

### Let's Add Damage to Hits

```text
!scriptcard {{
    --#sourceToken|@{selected|token_id}
    --#targetToken|@{target|token_id}
    --#title|Attack Roll
    --#emoteText|[*S:character_name] Attacks [*T:t-name]
    --=AttackRoll|1d20 + [*S:strength_mod] [STR] + [*S:pb] [PROF]
    --+Attack Roll Result|[$AttackRoll] vs Armor Class [roll][*T:t-bar2_value][/roll]
    --?[$AttackRoll] -ge [*T:t-bar2_value]|Hit|Miss

    --:Done|
    --X|
    --:Hit|
        --=DamageRoll|1d8 + [*S:strength_mod] [STR]
        --+HIT|The [*S:character_name]'s attack hits [*T:t-name] for [$DamageRoll] damage
    --^Done|
    --:Miss|
        --+MISS|The [*S:character_name]'s attack misses [*T:t-name]
    --^Done|
}}
```
![screenshot of Scriptcards output with damage roll on hits](images/tutorial1/step3-11_attackroll_damage.png)

#### Step 3-11 Explanation

### Let's Check for Critical Hits

```text
!scriptcard {{
    --#sourceToken|@{selected|token_id}
    --#targetToken|@{target|token_id}
    --#title|Attack Roll
    --#emoteText|[*S:character_name] Attacks [*T:t-name]
    --=AttackRoll|1d20 + [*S:strength_mod] [STR] + [*S:pb] [PROF]
    --+Attack Roll Result|[$AttackRoll] vs Armor Class [roll][*T:t-bar2_value][/roll]
    --?[$AttackRoll.Base] -eq 20|CriticalHit
    --?[$AttackRoll] -ge [*T:t-bar2_value]|Hit|Miss

    --:Done|
    --X|
    --:CriticalHit|
        --=DamageRoll|1d8 + [*S:strength_mod] [STR]
        --=CriticalDamage|1d8
        --=TotalDamage|[$DamageRoll] + [$CriticalDamage] [CRIT]
        --+CRITICAL|[*S:character_name]'s attack is a critical hit on [*T:t-name] for [$TotalDamage] damage
    --^Done|
    --:Hit|
        --=DamageRoll|1d8 + [*S:strength_mod] [STR]
        --+HIT|The [*S:character_name]'s attack hits [*T:t-name] for [$DamageRoll] damage
    --^Done|
    --:Miss|
        --+MISS|The [*S:character_name]'s attack misses [*T:t-name]
    --^Done|
}}
```
![screenshot of Scriptcards when attack rolls a natural 20](images/tutorial1/step3-12_attackroll_crit1.png)
![screenshot of Scriptcards when attack rolls a natural 20 showing additional damage roll](images/tutorial1/step3-12_attackroll_crit2.png)

#### Step 3-12 Explanation

## Recap and References
