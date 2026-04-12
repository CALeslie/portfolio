---
title: "666 Malevolence Lane"
#date: 2021-04-03T22:53:58+05:30
draft: false
github_link: "https://github.com/gurusabarish/hugo-profile"
badges:
  - Build Automation
imagelink:
  enable: true
  link: /projects/666-malevolence-lane
image: /images/mallane2.jpg
banner: /images/mallanebanner.png
description: ""
toc: 
---

## My Involvement

Writeup about the game and my involvement.

## Code Examples

Here I would post snippets of python code about the build automation work I've done, or examples of blueprints I made while working on systems of the game.

```
#Build function that runs once every 24 hours via APScheduler
def BuildGame(self):
        #Changes directory to UE5 build tools
        os.chdir(f"{self.epic_games_folder_path}/Engine/Build/BatchFiles/")

        #Builds the project's code into a target file
        exit_code = subprocess.call(f"Build.bat CapstonePrototype Win64 Development \"{self.game_uproject_path}\"")

        if not exit_code == 0:
            self.webhook.send(f"# Build Information\n\nFailed to build target file from source code! {alert_role_id}", thread=self.thread_id)
            return False

        #Starts build and package of project at game_uproject_path
        exit_code = subprocess.call(f"RunUAT BuildCookRun -project=\"{self.game_uproject_path}\" -platform=Win64 -clientconfig=Development -serverconfig=Development " +
                                    f"-skipcook -maps=AllMaps -compile -stage -pak -archivedirectory={self.build_folder_path} -archive", shell=True)
        
        #If exit_code anything other than 0 build failed, pings Camilla in discord
        if not exit_code == 0:
            self.webhook.send(f'# Build Information\n\nBuild failed! {alert_role_id}', thread=self.thread_id)
            return False
        else:
            data = BuildUtils.GetBuildJSONData()
            data["version"] += 1
            BuildUtils.StoreBuildJSONData(data)
            rel, mile, ver = data["release"], data["milestone"], data["version"]
            self.webhook.send(f'# Build Information\n\nBuild successful! Current version: v{rel}.{mile}.{ver} {alert_role_id}', thread=self.thread_id)
            return True
```