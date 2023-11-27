# Software requirements

We will use online tools for many of the Activities. However, some programs and softwares need to be downloaded and installed locally. Mac, Linux, and Windows versions are available. 

After downloading, test if the program is funktioning by opening it.

### Activity 1 and 2

*	**AliView** [AliView alignment viewer](http://ormbunkar.se/aliview) ([Larsson 2014 Bioinf](https://doi.org/10.1093/bioinformatics/btu531))

* **BMGE** (Block Mapping and Gathering with Entropy ([Criscuolo & Gribaldo 2010 BMC Evol Biol](https://doi.org/10.1186/1471-2148-10-210)). Download BMGE using the command below in your Terminal: 
	`curl ftp://ftp.pasteur.fr/pub/gensoft/projects/BMGE/BMGE-1.12.tar.gz -o BMGE-1.12.tar.gz`

	Extract the content of the compressed tar.gz file using:
	`tar xvzf BMGE-1.12.tar.gz `

	Change the directory to the BMGE folder and test the program:
	`cd BMGE-1.12`  
	`java -jar BMGE.jar `

	If you see a line starting with `mandatory parameters: -i ‘infile’…` the software works.
	

### Activity 3 and 4

* **FigTree** by Andrew Rambaut. Download the [`.dmg`](https://github.com/rambaut/figtree/releases/download/v1.4.4/FigTree.v1.4.4.dmg) for Mac users, [`.zip`](https://github.com/rambaut/figtree/releases/download/v1.4.4/FigTree.v1.4.4.zip) for Windows, or [`.tgz`](https://github.com/rambaut/figtree/releases/download/v1.4.4/FigTree_v1.4.4.tgz) for Linux.

	<details>
	  <summary>FigTree is not working... (click here)</summary>
	
	--------
	
	* For Windows/Linux users: try to fix by installing/updating to the newest java version.
	
	* For Mac users:
		* Try to fix by installing/updating to the newest java version. To do so, go to the terminal:
		`java -version` (Check the installed version)
		`brew install openjdk` (If you don't have homebrew, follow instructions [here](https://brew.sh/).
		To have openjdk in your PATH, run:
		`echo 'export PATH="/usr/local/opt/openjdk/bin:$PATH"' >> ~/.zshrc`
		`source ~/.zshrc`
		`java -version` (Check the installed version)
	
		* The app is till not responding? Update the "universalJavaApplicationStub".
		`cd /Applications/FigTree\ v1.4.4.app/Contents/MacOS`
		`curl -O https://raw.githubusercontent.com/tofi86/universalJavaApplicationStub/master/src/universalJavaApplicationStub`
		`chmod 755 universalJavaApplicationStub`
	
		* The app is till not responding? Try opening from the terminal:
		`java -jar /Applications/FigTree\ v1.4.4.app/Contents/Resources/Java/figtree.jar`
	
	--------
	</details>

### Activity 5 and 6

* **BEAST 2** ([Bouckaert et al. 2014](https://doi.org/10.1371/journal.pcbi.1003537)). **Download** the current BEAST 2 package (v2.7.6.) according to your platform from the [BEAST 2 website](http://www.beast2.org/). The BEAST 2 package includes BEAUti, BEAST 2 itself, TreeAnnotator, and other tools. As all these programs are written in Java, compilation is not required, and they should work on Mac OS X, Linux, and Windows. 

	After downloading the package, test if **BEAUti** (if there is a dialog "New Packages are available to install". Click **Yes**), **BEAST**, and **TreeAnnotator** can be opened. If not, try to update java if you haven't done so already (see above). 
	

* **Tracer** ([Rambaut et al. 2018](https://doi.org/10.1093/sysbio/syy032)). Download [Tracer](https://github.com/beast-dev/tracer/releases) according to your platform ([Mac OS X](https://github.com/beast-dev/tracer/releases/download/v1.7.2/Tracer.v1.7.2.dmg), [Linux](https://github.com/beast-dev/tracer/releases/download/v1.7.2/Tracer_v1.7.2.tgz), or [Windows](https://github.com/beast-dev/tracer/releases/download/v1.7.2/Tracer.v1.7.2.zip)) Just like BEAST 2, Tracer is written in Java and should work on any system.



