# scripts exported from Tecan Evoware

The roboRNA protocol is composed of one main script which is in turn calling several subroutines. In this directory you find the export / import files created with the Tecan Import/Export tool.

* `roboRNA_everything.exd` -- the complete export of labware, carriers, liquid classes, ROMA vectors and sub-scripts. (we removed some carriers and labware that are not used by the script but referenced in the deck layout).

* `roboRNA_part_*` -- separate exports of carriers, labware etc (including unused not found in the above everything export)

* `subroutines` folder:

  * separate exports of the complete set of MCA tip handling scripts -- see below

### Requirements

* Tecan Evo200 robot with ROMA (plate moving) and MCA96 (multichannel pipetting) arm
* on-board plate shaker (in our case a original Tecan shaker with two positions, only one needed)
* on-board plate heating position for drying after Ethanol washes of beads
* plate hotel space, in our case plates are stored in standard plate hotel "towers" at the back of the deck
* deepwell plates -- recommended are Greiner square well plates with round bottom for optimal mixing during shaking
* regular 96-well plates -- round well bottom recommended for optimal mixing
* matching plate lids (we use universal lids for deepwell plates)
* sterile 200 uL MCA tip racks in stacks of 8 (we use Axygen, clear)
* reagents and beads as described in our paper

### Installation

You will need to fine-tune and adapt these scripts to your own system. In particular you will probably use different carriers and labware. Moreover, all calls to subroutines need to be corrected to the actual file path where the routines end up in your own Evoware installation.

The following strategy may work:

1. Open roboRNA_everything.exd in the Tecan Import/Export tool.
2. Select everything for import
   - note that imported liquid classes cannot later be removed so, optionally, first exclude them from the import and see whether you want to replace any of the liquid classes in the script by liquid classes already existing in your system
3. Import, this will place the script files into your default Evoware scripts folder.
4. Create a sub-folder `subroutines` in your `Evoware\database\scripts` folder.
5. Move all scripts except the main RNAextraction script to that folder
6. Open the main script in Evoware Standard
7. Reconnect subroutine calls to their new location in your file system:
   - edit all calls to "shakeandwait", "fetch_tipcolumns", "drop_tipcolumns", and the MCA init or MCA reset scripts
   - open one by one all of the subroutine scripts in MCA_tips and look for subroutine calls within them, adapt file location
   - save all scripts


### MCA Tip Handling

Tecan Evoware has built-in tip management for their normal 8-span LiHa tips but does not seem to offer anything comparable for 96-channel MCA tip racks. So we implemented our own automated handling of stacked MCA tips. Main features:

* fetch between 2 and 12 columns of tips -- no need to specify positions or stacks
* tips are always fetched from the same (non-stacked) `MCA source` position
* tips are always dropped to the same (non-stacked) `MCA dest` position
* state of tips and stacks is remembered also between runs (stored on disc)
* automatic rotating-in of a new tip rack whenever needed -- no need to hard-code any of it
* startup methods to let user modify or reset tip and stack counts

This is how it looks:


https://user-images.githubusercontent.com/1862877/219094383-5f3ac4c9-0e8b-4bbb-abd5-74414654c334.mp4



Whenever the source position runs out of tips, a new tip rack will automatically be "rotated" in from a source stack and the rack with the used tips is stacked onto a waste stack. Those who want to inspect this tip handling in isolation, can import the two files from `subroutines/MCA_tips`. You will have to adapt all subroutine calls in the main script and in several subroutines in order to make things work on your own system.
 
Our script assumes that your MCA pipetting head can pick up partial sets of tips. On our own instrument, we had to remove  plastic clamps protruding right and left of the MCA in order to enable this functionality. Removing the clamps creates a slight risk of the MCA picking up more than one tip stack. Fine-tune the MCA vector definition on the MCA tip rack carrier to prevent that from happening.
