# BaseMod #
BaseMod provides a number of hooks and a console.

Currently supported version: `[EARLY_ACCESS_015]` (non beta)

## Requirements ##
#### General Use ####
* **Java 8 (do not use Java 9 - there is an issue with ModTheSpire right now on Java 9)**
* ModTheSpire v2.4.0+ (https://github.com/kiooeht/ModTheSpire/releases)

#### Development ####
* Java 8
* Maven
* CFR 124 (run this with Java 8, doesn't work well with 9)
* ModTheSpire (https://github.com/kiooeht/ModTheSpire)

## Building ##
1. (If you haven't already) `mvn install` ModTheSpire Altenatively, modify pom.xml to point to a local copy of the JAR.
2. Copy `desktop-1.0.jar` from your Slay the Spire folder into `../_lib` relative to the repo.
3. Decompile `desktop-1.0.jar` with `java -jar "cfr_0_124.jar" --comments false --showversion false --caseinsensitivefs true --outputdir "decompiled" --jarfilter com.megacrit.cardcrawl.* "desktop-1.0.jar"`
5. Run `mvn package`

## Installation ##
1. Copy `target/BaseMod.jar` to your ModTheSpire mods directory. Maven will automatically do this after packaging if your mods directory is located at `../_ModTheSpire/mods` relative to the repo.

# Wiki
Take a look at the wiki (https://github.com/daviscook477/BaseMod/wiki) to get started using BaseMod as either a console or a modding platform!

## Console ##
Take a look at the Console page on the wiki (https://github.com/daviscook477/BaseMod/wiki/Console) to start using the console to test things out!

## Known Issues ##
* If you use the console to `fight` an enemy or spawn an `event` in the starting room with Neow your save will be unloadable. Please refrain from using those commands until after leaving the starting room.
* If you use the `event` command with an invalid ID it will crash the game.
* BaseMod is likely to break when weekly patches hit. This means that if it's Thursday or Friday and things suddenly stop working you'll probably need to wait for an updated version of BaseMod in a day or two :)

## Contributors ##
* t-larson - Original author
* FlipskiZ - `hand` command, bug fixes
* daviscook477 - Custom players, custom colors, custom cards, more API hooks, code cleanup, bugfixes
* Haashi - custom potion support (w/ hooks for obtaining potions and relics) and dev console support for potions and powers
* BlankTheEvil - custom rendering for cards on a card-by-card basis and custom energy orb support

## Roadmap ##
* Keep up-to-date with weekly patches to keep mods useable
* More tools/more intuitive tools for mods to create custom UIs
* CustomMonster class so modders can create new monsters with new animations
* Have a feature request? Make an issue: (https://github.com/daviscook477/BaseMod/issues)

## For Modders ##

### Hooks ###
Take a look here for the hooks that are available (https://github.com/daviscook477/BaseMod/wiki/Hooks)

### Mod Badges ###
Take alook here for how to set up a Mod Badge (https://github.com/daviscook477/BaseMod/wiki/Mod-Badges)

### Custom Relics ###
* `CustomRelic(String id, Texture texture, AbstractRelic.RelicTier tier, AbstractRelic.LandingSound sfx)`
* `BaseMod.addRelic(AbstractRelic relic, RelicType type)` (note: `CustomRelic` extends `AbstractRelic`) and `RelicType` indicates if this relic is shared between both characters or `RED` only or `GREEN` only.
* `BaseMod.removeRelic(AbstractRelic relic, RelicType type)` removes a relic from the game (note: removing a relic used by an event is currently untested/undefined behavior)
* `BaseMod.removeRelic(AbstractRelic relic)` removes a relic from the game without having to know its `RelicType`

### Custom Cards ###
* `CustomCard(String id, String name, String img, int cost, String rawDescription, CardType type, CardColor color, CardRarity rarity, CardTarget target, int cardPool)`
* `BaseMod.addCard(AbstractCard card)` (note: `CustomCard` extends `AbstractCard`).
* `BaseMod.removeCard(AbstractCard card)` removes a card from the game (note: removing a card used by an event is currently untested/undefined behavior)

### Custom Player Characters ###
The process for creating custom player characters is fairly involved but still not too complex. It is detailed below:

1. To add a custom character there are two major steps. You need to register both a new color and a new character.  Basically the Ironclad is RED, the Silent is GREEN, the unused content Crowbot is BLUE, etc... In making a custom character you would want to make a new color like maybe YELLOW or PURPLE or ORANGE.
2. Since the base game represents colors using an enum we need to use ModTheSpire's enum patching feature. To do this create any class and add to it the following code:
```
	@SpireEnum
	public static AbstractCard.CardColor MY_NEW_COLOR;
```
3. The base game also reprents players using an enum so we must do the same thing for the player enum. Use this code:
```
	@SpireEnum
	public static AbstractPlayer.PlayerClass MY_NEW_PLAYER_CHARACTER;
```
3. To create a new color use `BaseMod.addColor`. This should be called in your mod's `initialize` method. It does not need a special handler to work. The parameters are as follows: `String color` (this should be `MY_NEW_COLOR.toString()`), `Color bgColor` (the background color for the card color), `Color backColor` (the back color for the card color), `Color frameColor` (the frame color for the card color), `Color frameOutineColor` (the frame outline color for the card color), `Color descBoxColor` (the description box color), `Color trailVfxColor` (the trail vfx color), `Color glowColor` (the glow color), `String attackBg` (path to your attack bg texture for the card color, path starts relative to your `SlayTheSpire` folder), `String skillBg` (path to your skill bg texture for the card color, path starts relative to your `SlayTheSpire` folder), `String powerBg` (path to your power bg texture for the card color, path starts relative to your `SlayTheSpire` folder), `String energyOrb` (path to your energy orb texture for the card color, path starts relative to your `SlayTheSpire` folder)
4. To create a new player character make a EditCharacterSubscriber and in the `receiveEditCharacters` method go ahead and call `BaseMod.addCharacter`. The parameters are as follows: `Class characterClass` (the actual java *Class* of your character, e.g. `MyCharacterClass.class`), `String titleString` (title string for the character), `String classString` (class string for the character), `String color`, (the color for this character; should be `My_New_Color.toString()`), `String selectText` (select text for the character), `String selectButton` (path to select button texture starting relative to the `SlayTheSpire` folder), `String portrait` (path to portrait texture starting relative to the `SlayTheSpire` folder), `String characterID` (this should be `MY_NEW_PLAYER_CHARACTER.toString()`)
5. Now just be sure to add some cards for your custom character! When defining cards for your custom character rather than using `AbstractCard.CardColor.WHATEVER` go ahead and use `MY_NEW_COLOR` instead.
