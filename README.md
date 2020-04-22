-- Intro sequence script (data pre-load)
-- Andrew Bereza

local Player = game.Players.LocalPlayer

local Allowed = game.ReplicatedStorage.IsWhitelisted:InvokeServer()

if not Allowed then
	script.Closed.Enabled = true
	script.Closed.Parent = script.Parent
elseif Player:FindFirstChild("DataLoaded") == nil then
	
	spawn(function()
		repeat wait() until Player.Character
		Player.Character.PrimaryPart.Anchored = true
		Player.Character.Torso.CFrame = CFrame.new(workspace.Center.Position)
	end)	
	
	local Teleported = false
	local TimeStamp 
	game:GetService("TeleportService").LocalPlayerArrivedFromTeleport:connect(function(Gui,Data)
		if Data and Data.Express == true then
			Teleported = true
			TimeStamp = Data.TimeStamp
		end
	end)
	
	if Teleported then -- Give them a faster load in if they teleported in
		
		game.Players.LocalPlayer:WaitForChild("PlayerGui")
		local GUI = script.GUI
		GUI.Enabled = true
		GUI.Parent = game.Players.LocalPlayer.PlayerGui
		wait()
		
		local Success
		local Error
	

	
	else

		local PlayerGui = script.Parent

		local IntroGui = script.IntroGui
		IntroGui.Parent = PlayerGui
		IntroGui.Enabled = true
		script.Parent.ScreenGui.Enabled = false
		
		local Enabled = true
		local loadingin = false
		
		IntroGui:WaitForChild("Play")


		IntroGui.Play.MouseEnter:connect(function()
			if IntroGui.Play.ImageColor3 == Color3.new(1,1,1) then
				IntroGui.Play.ImageColor3 = Color3.new(117/255, 1, 20/25)
			end
		end)
		IntroGui.Play.MouseLeave:connect(function()
			if IntroGui.Play.ImageColor3 == Color3.new(117/255, 1, 20/25) then
				IntroGui.Play.ImageColor3 = Color3.new(1,1,1)
			end	
		end)
		
		
		local function LoadIn()
			if not loadingin then
				loadingin = true
				IntroGui.Play.ImageColor3 = Color3.new(1, 190/255, 58/255)
				IntroGui.Status.TextColor3 = Color3.new(1, 190/255, 58/255)
				IntroGui.Status.Text = "Loading player data."
				IntroGui.Status.Visible = true	
				
				local Success,Error = game.ReplicatedStorage.LoadData:InvokeServer()		
				
				if Success then
					IntroGui.Status.Text = "Success! Entering game..."
					IntroGui.Status.TextColor3 = Color3.new(0.5,1,0.5)
				else
					print(Error)
					IntroGui.Status.Text = "Loading failed! ROBLOX servers may be down. Try again later."
					IntroGui.Status.TextColor3 = Color3.new(1,0.5,0.5)
				end
				
				repeat wait(0.1) until game.Players.LocalPlayer:FindFirstChild("DataLoaded") ~= nil
		
				local function tween(Object, Properties, Value, Time)
					Time = Time or 0.5
				
					local propertyGoals = {}
					
					local Table = (type(Value) == "table" and true) or false
					
					for i,Property in pairs(Properties) do
						propertyGoals[Property] = Table and Value[i] or Value
					end
					local tweenInfo = TweenInfo.new(
						Time,
						Enum.EasingStyle.Linear,
						Enum.EasingDirection.Out
					)
					local tween = game:GetService("TweenService"):Create(Object,tweenInfo,propertyGoals)
					tween:Play()
				end
				
				
				tween(game.Lighting.IntroBlack,{"Brightness"},-1,1)
			
				Enabled = false
				game.StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All,true)
			end
		end
		
		IntroGui.Play.MouseButton1Click:connect(LoadIn)		
		

		
		game.StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All,false)
			if game.PlaceId == 455705058 then
				IntroGui.Update.Text = "Testing Environment"
			end
		local starttime = tick()
		
		repeat wait() until workspace.CurrentCamera ~= nil
		
		IntroGui.Play.ImageTransparency = 1
		
		local X = workspace.CurrentCamera.ViewportSize.X + 10
		local Y = workspace.CurrentCamera.ViewportSize.Y + 40
		local XYImgSize = 1.77777777778 -- What X/Y SHOULD be
		local XYRatio = X/Y -- What X/Y IS
		if XYRatio < XYImgSize then
			-- X/Y = 1.7..
			-- X = 1.7.. * Y
			X = math.ceil(XYImgSize * Y)
		end

		local Player = game.Players.LocalPlayer

		print(starttime - tick())
		
		for i=1,30 do
			wait()

			IntroGui.LoadingText.TextTransparency = IntroGui.LoadingText.TextTransparency + 1/30
			IntroGui.LoadingText.TextStrokeTransparency = IntroGui.LoadingText.TextStrokeTransparency + 1/30
		end
		
		wait(0.5)
		
		IntroGui.Play.Visible = true
		
		
		for i=1,20 do
			wait()
			IntroGui.Update.TextTransparency = IntroGui.Update.TextTransparency - 1/20
			IntroGui.Update.TextStrokeTransparency = IntroGui.Update.TextStrokeTransparency - 1/20
		end
		for i=1,15 do
			wait()
			IntroGui.Play.ImageTransparency = IntroGui.Play.ImageTransparency - 1/15
		end
		if game:GetService("UserInputService").GamepadEnabled then
			IntroGui.Play.Xbox.Visible = true
		end
		
		game:GetService("UserInputService").InputBegan:connect(function(Input)
			if Input.UserInputType == Enum.UserInputType.Gamepad1 or Input.UserInputType == Enum.UserInputType.Gamepad2 or Input.KeyCode == Enum.KeyCode.ButtonA then
				LoadIn()
			end
		end)
	end
else
	script:Destroy()
end
