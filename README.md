# Hooks for CE 1.4.1
If you are an add-on developer and want to make use of these hooks in your code, contact me and I'll give you access to the repo that has the code. I'm keeping it private as I don't wanna publish code that's not mine.

Base Class edits:
```
Item
Block
SlotArmor
GuiIngame    	
FCBlockLeaves
WorldGenShrub
WorldGenForest
FCUtilsTrees    	
FCWorldGeneratorBonusBasket
ComponentScatteredFeatureSwampHut
ComponentScatteredFeatureDesertPyramid
ComponentScatteredFeatureJunglePyramid
ComponentMineshaftCorridor
WorldGenDungeons
ComponentVillageHouse2
ComponentStrongholdChestCorridor
ComponentStrongholdLibrary
ComponentStrongholdRoomCrossing    	
BiomeGenBase
GenLayerRiverMix
GenLayerShore
```
This "small" utility add-on adds the following hooks:

## Valid for Armor Slot
Added the boolean ```isValidForArmorSlot()``` in ```Item.java``` and ```Block.java```.
It passes along the ```ItemStack``` and ```ArmorType``` ie which armor slot (0: Helmet, 1: Chest, 2: Legs, 3: boots)

## Blur Overlay
- Added the method ```getBlurOverlay()``` to allow the display of a overlay like when wearing pumpkins by returning the String of the texture location.

  Example: 
  ```java
   return "%blur%/misc/pumpkinblur.png"
  ```

- Added the boolean ```showBlurOverlayWithGuiDisabled()``` which determines if the overlay should be shown when the GUI is disabled with F1.

Both methods have pass along the ```ItemStack```.

## Replaced by Leaves
Added the boolean ```canBeReplacedByLeaves()``` in ```Blocks.java``` to specify if Leaves can replace this block when generated.

The following method is used to do the check:
```java
public static boolean canLeavesBePlaced(World world, int x, int y, int z)
{
  int blockID = world.getBlockId(x, y, z);
     
  if (blockID == 0 || (!Block.opaqueCubeLookup[blockID] && Block.replaceableByLeavesLookup[blockID]) )
  {
    return true;
  }
  
  return false;
}
```
## Biomes
Splits up the River and Beach Biomes into the variant of the biome they are in.
The actual generation isn't touched. It just makes it possible for add-on makers to generate world decorations specifically in one river/beach biome.

Added following River Biomes:
- riverPlains
- riverDesert
- riverExtremeHills
- riverForest
- riverTaiga
- riverSwamp
- riverJungle
- riverMushroomIsland

Added following Beach Biomes:
- beachPlains
- beachDesert
- beachForest
- beachTaiga
- beachJungle

## Loot Chests/Baskets
- ``` addBonusBasketLoot() ```
  Passes along the ArrayList of the bonus basket loot. 
 
  Example:
  ```java
  // FCUtilsRandomItemStack ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  bonusBasket.add( new FCUtilsRandomItemStack( FCBetterThanWolves.fcItemGoldenDung.itemID, 0, 1, 1, 10 ) );
  ```

- ``` addWitchHutLoot() ```
  Passes along the ArrayList of the witch hut basket loot. 
  
  Example:
  ```java
  // FCUtilsRandomItemStack ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  basketLoot.add( new FCUtilsRandomItemStack( Item.glassBottle.itemID, 0, 2, 8, 10 ) );
  ```
  
- ``` addDesertWellLoot() ```
  Passes along the ArrayList of the desert well basket loot. 
  
  Example:
  ```java
  // FCUtilsRandomItemStack ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  basketLoot.add( new FCUtilsRandomItemStack( FCBetterThanWolves.fcItemHempSeeds.itemID, 0, 1, 4, 5 ) );
  ```
  
- ``` addDesertPyramidLoot() ```
  Passes along the ArrayList of the desert temple loot when the temple is not looted and the ArrayList of the desert temple when looted. 
  
  Example:
  ```java
  // WeightedRandomChestContent ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  chestLoot.add( new WeightedRandomChestContent( Item.helmetGold.itemID, 0, 1, 1, 5 ) );
  
  lootedChestLoot.add( new WeightedRandomChestContent( Item.bone.itemID, 0, 4, 6, 20 ) );
  ```
  
- ``` addJunglePyramidLoot() ```
  Passes along the ArrayList of the jungle temple loot when the temple is not looted and the ArrayList of the desert temple when looted. 
  
  Example:
  ```java
  // WeightedRandomChestContent ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  chestLoot.add( new WeightedRandomChestContent( Item.pickaxeGold.itemID, 0, 1, 1, 2 ) );
  
  lootedChestLoot.add( new WeightedRandomChestContent( Item.bowlEmpty.itemID, 0, 1, 1, 10 ) );
  ```
  
- ``` addMinecartLoot() ```
  Passes along the ArrayList of the minecart loot in abandoned mineshafts 
  
  Example:
  ```java
  // WeightedRandomChestContent ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  minecartLoot.add(new WeightedRandomChestContent(Item.bread.itemID, 0, 1, 3, 15) );
  ```
  
- ``` filterMinecartContents() ```
  Passes along the minecart Entity. This allows to filter the contents and remove items eg depending at what world height the minecart was generated.
  Check out in ```FCAddOn.java``` which Items are already being filtered.
  
  Example:
  ```java
  for ( int slot = 0; slot < minecart.getSizeInventory(); slot++ )
  {
    ItemStack tempStack = minecart.getStackInSlot( slot );
    if ( tempStack != null )
    {
      int itemID = tempStack.itemID;
      
      if ( itemID == Item.ingotIron.itemID )
      {
        if ( minecart.posY > 36F )
        {
          minecart.setInventorySlotContents( slot, null );
        }
        else
        {
          tempStack.stackSize = 1;
        }
      }
    }
  }
  ```
  
- ``` addDungeonChestLoot() ```
  Passes along the ArrayList of the minecart loot in abandoned mineshafts 
  
  Example:
  ```java
  // WeightedRandomChestContent ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  chestLoot.add( new FCUtilsRandomItemStack(Item.saddle.itemID, 0, 1, 1, 1 ) );
  ```
  
- ``` filterDungeonChestContents() ```
  Passes along the world and chest coords. This allows to filter the contents and remove items eg depending at what world height the chest was generated.
  Check out in ```FCAddOn.java``` which Items are already being filtered.
  
  Example:
  ```java
  TileEntityChest chestEnt = (TileEntityChest)world.getBlockTileEntity( iChestI, iChestJ, iChestK );
  
  if ( chestEnt != null )
  {
    for ( int iSlot = 0; iSlot < chestEnt.getSizeInventory(); iSlot++ )
    {
      ItemStack tempStack = chestEnt.getStackInSlot( iSlot );
      
      if ( tempStack != null )
      {
        int iItemID = tempStack.itemID;
        
        if ( iItemID == Item.ingotIron.itemID )
        {
          if ( iChestJ > 36 )
          {
            chestEnt.setInventorySlotContents( iSlot, null );
          }
          else
          {
            tempStack.stackSize = 1;
          }
        }
      }
    }
  }
  ```
  
- ``` addBlacksmithChestLoot() ```
  Passes along the ArrayList of the blacksmith chest loot. It only is generated in non abandoned villages.
  
  Example:
  ```java
  // WeightedRandomChestContent ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  chestLoot.add( new WeightedRandomChestContent(Item.ingotIron.itemID, 0, 1, 5, 10) );
  ```
  
- ``` addStrongholdChestLoot() ```
  Passes along the ArrayList of the chest loot in stronghold corridors.
  
  Example:
  ```java
  // WeightedRandomChestContent ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  chestLoot.add( new WeightedRandomChestContent(Item.ingotIron.itemID, 0, 1, 5, 10) );
  ```
  
- ``` addStrongholdLibraryChestLoot() ```
  Passes along the ArrayList of the chest loot in stronghold library.
  
  Example:
  ```java
  // WeightedRandomChestContent ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  chestLoot.add( new WeightedRandomChestContent(Item.ingotIron.itemID, 0, 1, 5, 10) );
  ```
  
- ``` addStrongholdCrossingChestLoot() ```
  Passes along the ArrayList of the chest loot in stronghold crossings.
  
  Example:
  ```java
  // WeightedRandomChestContent ( itemID, itemDamage, minStackSize, maxStackSize, weight)
  chestLoot.add( new WeightedRandomChestContent(Item.ingotIron.itemID, 0, 1, 5, 10) );
  ```
