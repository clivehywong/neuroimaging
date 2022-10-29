Installation with Perpetual License (Mac)
=========================================

1. Get the license from the MathWork webpage.
2. Download Product Installer.
3. Start the Product Installer. Note the 4 options from the "Advanced Option" Menu.
4. The default is "I want to do a standard installation". We will come back later.
5. Install the MATLAB and toolboxes, with the license file.
6. Start the Product Installer again.
7. From the Advanced Option menu, choose "I want to install network license manager".
8. Install Network License Manager with the license file.
9. Open Terminal.
10. cd /Applications/MATLAB_R2022b.app/
11. less etc/license.dat
12. copy the line "SERVER xxxx xxxx 27000"
13. nano licenses/network.lic
14. Replace "SERVER server_name hostid port_number" with the line copied in step 12.
15. Start the license with "maci64/lmgrd"
16. Start MATLAB.

SPM mex files
-------------

Run this:
find . -name "*.mexmaci64" -exec xattr -d com.apple.quarantine {} \;
