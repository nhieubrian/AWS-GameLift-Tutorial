# AWS GameLift with Unreal Engine

## Installation:

- [How to install Unreal Engine Source](https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/ProgrammingWithCPP/DownloadingSourceCode/)
    - **Troubleshooting:**
    - Associate your GitHub account to your Unreal Account (IMPORTANT)
    - Be sure to use the correct version of Visual Studios
- [How to install AWS CLI](https://aws.amazon.com/cli/)
    - [Find your access keys](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html)
    ```
    aws configure
    ```
    - Type in your access keys, choose Default region name: $(REGION_NAME), and Default output format: json 
    - [How to find available GameLift regions](https://aws.amazon.com/gamelift/faq/). I will be using, **us-west-2**, which is based in Oregon.

## Start Up:
* Guide to begin accessing the Test Project: *

- Right click the GameLiftTutorial.uproject and generate the Visual Studios project.
- Select Development Editor, Win64 and build the project.
- This time select Development Server, Win64 and build again.
- Launch the GameLiftTutorial.uproject (or rebuild the development editor)
- On the top left, File -> Package Project -> Build Target -> GameLiftTutorial
- Then package the project, File -> Package Project -> Win64 (can choose any build, just specify when uploading to AWS)

## Uploading to AWS
* Packaging the game files to AWS and allowing AWS to launch a light-weight EC2 desktop with the server executable *

- [Download VC_Redist (Choose the installation version, x64)](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)
- Navigate to WindowsServer inside the game folder, drag and drop the installation executable into the folder.
- Create a install.bat file inside the folder and add these commands
    ```
    VC_redist.x64.exe /q
    Engine\Extras\Redist\en-us\UE4PrereqSetup_x64.exe /q
    ```
- Open a terminal and use this command. 
    ```
    aws gamelift upload-build --name GameLiftTutorial --build-version 1.0.0 --build-root $(FILE_PATH_TO_SERVER_FILES) --operating-system WINDOWS_2012 --region $(REGION_NAME)
    ```

    - Operating system is based on the build of the packaged Unreal Engine game, ex. Windows above.
    - $(REGION_NAME) - I have been using **us-west-2**

## Creating the Fleet, Queue and Matchmaking

- Navigate to the AWS GameLift dashboard
![Image](./images/f41b229155b81a81f7dc381b59560d0a.png)
- You will see that your packaged server is on AWS, but now we need to create the **Fleet:**
    - Name: $(NAME)
    - Fleet Type: Choose $(ON-DEMAND) for reserved resources for your server, or $(SPOT) for leftover resources (This is Cheaper!)
    - Build: $(GameLiftTutorial 1.00 ...)
    - Home Region: Ensure that this region is the same as what you used to upload earlier
    - *Leave everything else as default*
    - Launch Path: \game\GameLiftTutorial\Binaries\Win64\GameLiftTutorialServer.exe
        - Parameters: -port=7777
        - Concurrent Processes: 1
        - ** Don't forget the green check mark! **
    - EC2 Port Range: 7777
        - Protocol: UDP
        - IP Address: 0.0.0.0/0
    - Click Submit! This will take a couple of minutes to create.
-     



## Credits to Flopperam:

[Link to Tutorial](https://www.youtube.com/watch?v=tOy0xYaP3wA&list=PLa1dM5bPQv0u2IWZRIxtRqwWVJNOUtlbF)

