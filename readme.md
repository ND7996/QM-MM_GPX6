***Prepare structures of CYS for GPX6 mouse and human***

Stucture 1 - SH + H2O2   - thiol    

Structure 2 - S- + H2O2  - thiolate 

Stucture 3 - SOH - sulfenic acid

***Running Amber on the structures***

**PREP**

1 Command - pdb4amber -i MouseCysGSSG.pdb -o MouseCysGSSG.pdb4amber.pdb
#The resulting PDB file should now contain all of the hydrogens for each of the residues.

2 Command - antechamber -fi pdb -fo prepi -i GLO.pdb -o DM1.prepi -c bcc -at amber -nc 0 -rn GLO

#fi and fo define the format of the input and output files, c specifies the model for the point charges, 
and at specifies the atom type style for the molecule. -nc is net molecular charge.  -rn  residue name

OUTPUT
This quantum mechanics program allows the optimization of the molecule.
After that, SQM performs a Mulliken population analysis and adds an empirical bond charge correction (bcc) term to 
compute the final BCC point charges.

3 Command - parmchk2 -f prepi -i GLO.prepi -o GLO.frcmod

OUTPUT
GLO.prep.pdb, GLO.prepi, GLO.frcmod

The prepi file contains
. Header. It contains a line for comments and the definition of the name of the residue 
. List of atoms. It is constituted by 11 columns: atom id (1), atom label (2), atom type (3),
connectivity type (4), connectivity tree (5-7; all atoms referred to the three initial dummy
. Loops. 
. Impropers.

The frcmod file contains
1. Mass
2. Bonds
3. Angles
4. Dihedrals
5. Improper

Input for tleap

source leaprc.gaff2
source leaprc.water.tip3p
loadamberParams GLO.frcmod                                            !Load the additional frcmod file for ligand
loadAmberPrep GLO.prepi                                               !Load the parameters file for ligand
sys = loadPDB 6kn4-state1.pdb                                         !Load PDB file
savePDB sys 6kn4-state1_vac.pdb                                       !Save AMBER format PDB file
SaveAmberParm sys 6kn4-state1_vac.prmtop 6kn4-state1_vac.inpcrd       !Save AMBER topology and coordinates in vacuum
addIons2 sys Na+ 0                                                    !Add Sodium till neutral system
1addIons2 sys Cl- 0                                                   !Add Chlorine till neutral system
SolvateOct sys TIP3PBOX 13                                            !Solvate with octahedral box with dimensions of
savePDB sys 6kn4-state1_leap.pdb                                      !Save Solvated AMBER PDB
SaveAmberParm sys 6kn4-state1.prmtop 6kn4-state1.inpcrd               !Save solvated AMBER topology and coordinates
quit

**MINIMIZATION**

Run min1.in

simple generalized Born minimization script
 &cntrl
   imin=1, ntb=0, maxcyc=100, ntpr=10, cut=1000., igb=8, 
 /


 **HEATING**

 Implicit solvent initial heating mdin
 &cntrl
   imin=0, irest=0, ntx=1,
   ntpr=1000, ntwx=1000, nstlim=100000,
   dt=0.002, ntt=3, tempi=10,
   temp0=300, gamma_ln=1.0, ig=-1,
   ntp=0, ntc=2, ntf=2, cut=1000,
   ntb=0, igb=8, ioutfm=1, nmropt=1,
 /
 &wt
   TYPE='TEMP0', ISTEP1=1, ISTEP2=100000,
   VALUE1=10.0, VALUE2=300.0,
 /
 &wt TYPE='END' /


 **EQUILIBRATION AND PRODUCTION**


 Implicit solvent molecular dynamics
 &cntrl
   imin=0, irest=1, ntx=5,
   ntpr=1000, ntwx=1000, nstlim=500000,
   dt=0.002, ntt=3, tempi=300,
   temp0=300, gamma_ln=1.0, ig=-1,
   ntp=0, ntc=2, ntf=2, cut=1000,
   ntb=0, igb=8, ioutfm=1,
 /



