# tx2_0

A collection of all the steps needed to create the nominal 2 degree global configuration for CESM/MOM6.

## Creating, building, and running a case

1. Download the latest CESM sandbox:
   ```
   git clone https://github.com/ESCOMP/CESM.git cesm3_0_alpha09b_tx2_0
   cd cesm3_0_alpha09b_tx2_0
   git checkout cesm3_0_alpha09b
   ./bin/git-fleximod update
   ```

2. Modify `ccs_config` (this step should not be required once the changes are merged):
   ```
   cd ccs_config
   git checkout -b tx2_0
   git pull https://github.com/chengz2/ccs_config_cesm.git tx2_0
   ```

3. Create an ocean only case:
   ```
   [PATH-TO-SANDBOX]/cime/scripts/create_newcase --case g.e30_a09b.GW_JAR.TL319_t201_wgx3_hycom1_N75.2026.??? --res TL319_t201_wg37 --compset GW_JRA --run-unsupported
   ```
   or create a fully coupled case:
   ```
   [PATH-TO-SANDBOX]/cime/scripts/create_newcase --case b.B1850C_LTso.ne16pg3_t201_hycom1_N75.2026.??? --res ne16pg3_t201 --compset B1850C_LTso --run-unsupported
   ```
   Replace `???` with you case number.

4. Modify MOM6 settings (these settings should be out of box at some point):
   - Copy `/glade/work/chengz/cesm.cases/tx2_0/g.e30_a09b.GW_JAR.TL319_t201_wgx3_hycom1_N75.2026.001/SourceMods/src.mom/MOM_input` to `SourceMods/src.mom`
   - Modify vertical grid file configuration: `./xmlchange MOM6_VERTICAL_GRID=hycom1`
   - Modify `diag_table` as needed

5. Modify CICE settings (these settings should be out of box at some point):
   - Copy `/glade/work/chengz/cesm.cases/tx2_0/g.e30_a09b.GW_JAR.TL319_t201_wgx3_hycom1_N75.2026.001/user_nl_cice` to `$CASEROOT`

6. Sample PE layout for ocean only (this will run for about 60 years per day):
   ```
   Comp  NTASKS  NTHRDS  ROOTPE PSTRIDE
   CPL :    128/     1;      0      1 
   ATM :    128/     1;      0      1 
   LND :      1/     1;      0      1 
   ICE :    128/     1;      0      1 
   OCN :    896/     1;    256      1 
   ROF :    128/     1;      0      1 
   GLC :      1/     1;      0      1 
   WAV :    128/     1;    128      1 
   ESP :      1/     1;      0      1 
   ESMF_AWARE_THREADING is False
   ROOTPE is with respect to 128.0 tasks per node
   ```
   Sample PE layout for fully coupled:
   ```
   CPL :    768/     1;      0      1 
   ATM :    768/     1;      0      1 
   LND :    768/     1;      0      1 
   ICE :    768/     1;      0      1 
   OCN :    768/     1;      0      1 
   ROF :    768/     1;      0      1 
   GLC :      1/     1;      0      1 
   WAV :    128/     1;      0      1 
   ESP :      1/     1;      0      1 
   ```

7. Setup, build, and submit the case:
   ```
   ./case.setup
   qcmd -- ./case.build
   ./case.submit
   ```

