--[[
    Documentation:
    function Library:Window(Data: table
        Title/title: string,
        Accent/accent: string,
        Info/info: string,
        Width/width: number,
        Height/height: number
    )

    function Window:Tab(Data: table
        Name/name: string
    )

    function Tab:Section(Data: table
        Name/name: string,
        Side/side: number
    )

    function Section:Toggle(Data: table
        Name/name: string,
        Default/default: boolean,
        Flag/flag: string,
        Callback/callback: function
    )

    function Section:Slider(Data: table
        Name/name: string,
        Min/min: number,
        Max/max: number,
        Default/default: number,
        Flag/flag: string,
        Callback/callback: function
    )

    function Section:Dropdown(Data: table
        Name/name: string,
        Items/items: table,
        Default/default: number,
        Flag/flag: string,
        Callback/callback: function
    )

    function Section:Textbox(Data: table
        Name/name: string,
        Default/default: string,
        Flag/flag: string,
        Callback/callback: function
    )

    function Section:Label(Data: table
        Name/name: string
    )

    function Section:Colorpicker(Data: table
        Name/name: string,
        Default/default: Color3,
        Alpha/alpha: number,
        Flag/flag: string,
        Callback/callback: function
    )

    function Toggle:Keybind(Data: table
        Default/default: EnumItem,
        Mode/mode: number,
        Flag/flag: string
    )

    function Toggle:Colorpicker(Data: table
        Default/default: Color3,
        Alpha/alpha: number,
        Flag/flag: string,
        Callback/callback: function
    )

    function Label:Colorpicker(...)  same as Toggle:Colorpicker
    function Window:SetAccent(Color3)
    function Window:KeybindList()
]]

local LoadingTick = os.clock()

if getgenv and getgenv().Library then
    getgenv().Library:Unload()
end

local Library do
    local RunService = game:GetService("RunService")
    local TextService = game:GetService("TextService")
    local HttpService = game:GetService("HttpService")
    local UserInputService = game:GetService("UserInputService")
    local Workspace = game:GetService("Workspace")
    local CoreGui = cloneref and cloneref(game:GetService("CoreGui")) or game:GetService("CoreGui")

    gethui = gethui or function()
        return CoreGui
    end

    local LocalPlayer = game:GetService("Players").LocalPlayer
    local Mouse = LocalPlayer:GetMouse()

    local FromRGB = Color3.fromRGB
    local FromHSV = Color3.fromHSV
    local ColorNew = Color3.new

    local RGBSequence = ColorSequence.new
    local RGBSequenceKeypoint = ColorSequenceKeypoint.new

    local NumSequence = NumberSequence.new
    local NumSequenceKeypoint = NumberSequenceKeypoint.new

    local UDim2New = UDim2.new
    local UDim2Offset = UDim2.fromOffset
    local UDim2Scale = UDim2.fromScale
    local UDimNew = UDim.new
    local Vector2New = Vector2.new

    local InstanceNew = Instance.new

    local MathClamp = math.clamp
    local MathFloor = math.floor
    local MathMin = math.min
    local MathMax = math.max

    local TableInsert = table.insert

    local StringFormat = string.format
    local StringLower = string.lower

    Library = {
        Flags = { },
        SetFlags = { },

        Theme = {
            ["Accent"] = FromRGB(190, 162, 227),
            ["Glint"] = FromRGB(255, 255, 255),
            ["Fill"] = FromRGB(13, 13, 13),
            ["Inner"] = FromRGB(29, 29, 29),
            ["Outer"] = FromRGB(0, 0, 0),
            ["Bar Top"] = FromRGB(22, 22, 22),
            ["Bar Bottom"] = FromRGB(13, 13, 13),
            ["Dark"] = FromRGB(12, 12, 12),
            ["Section"] = FromRGB(19, 19, 19),
            ["Hover"] = FromRGB(38, 38, 38),
            ["Text"] = FromRGB(204, 204, 204),
            ["Dim"] = FromRGB(85, 85, 85)
        },

        Sizes = {
            Tab = 12,
            Title = 12,
            TitleSpacing = 2
        },

        Shimmer = {
            Enabled = true,
            Speed = 25,
            Width = 60,
            Glow = 255
        },

        Directory = "coolui",

        -- Ignore below
        Connections = { },
        Threads = { },

        UnnamedConnections = 0,
        UnnamedFlags = 0,

        ScreenGui = nil,
        Font = nil
    }

    Library.__index = Library
    Library.Tabs = { }
    Library.Tabs.__index = Library.Tabs
    Library.Sections = { }
    Library.Sections.__index = Library.Sections
    Library.Toggles = { }
    Library.Toggles.__index = Library.Toggles
    Library.Labels = { }
    Library.Labels.__index = Library.Labels

    local Theme = Library.Theme
    local Sizes = Library.Sizes

    local KeyNames = {
        ["LeftShift"] = "ls", ["RightShift"] = "rs", ["LeftControl"] = "lc", ["RightControl"] = "rc",
        ["LeftAlt"] = "la", ["RightAlt"] = "ra", ["Space"] = "spc", ["Return"] = "ent", ["Backspace"] = "bksp",
        ["Tab"] = "tab", ["CapsLock"] = "caps", ["Insert"] = "ins", ["Delete"] = "del", ["Home"] = "home",
        ["End"] = "end", ["PageUp"] = "pgup", ["PageDown"] = "pgdn", ["Up"] = "up", ["Down"] = "dwn", ["Left"] = "lft", ["Right"] = "rgt"
    }

    Library.Multiply = function(self, Color, Factor)
        return ColorNew(Color.R * Factor, Color.G * Factor, Color.B * Factor)
    end

    Library.KeyName = function(self, Key)
        if Key == nil then
            return "-"
        end

        if type(Key) == "string" then
            return Key
        end

        return KeyNames[Key.Name] or StringLower(Key.Name)
    end

    Library.Round = function(self, Number, Float)
        local Multiplier = 1 / (Float or 1)
        return MathFloor(Number * Multiplier) / Multiplier
    end

    Library.Thread = function(self, Function)
        local NewThread = coroutine.create(Function)

        coroutine.wrap(function()
            coroutine.resume(NewThread)
        end)()

        TableInsert(self.Threads, NewThread)
        return NewThread
    end

    Library.SafeCall = function(self, Function, ...)
        if not Function then
            return
        end

        local Success, Result = pcall(Function, ...)

        if not Success then
            warn(Result)
            return false
        end

        return Success
    end

    Library.Connect = function(self, Event, Callback, Name)
        Name = Name or StringFormat("Connection_%s", self.UnnamedConnections + 1)
        self.UnnamedConnections = self.UnnamedConnections + 1

        local NewConnection = {
            Event = Event,
            Callback = Callback,
            Name = Name,
            Connection = Event:Connect(Callback)
        }

        TableInsert(self.Connections, NewConnection)
        return NewConnection
    end

    Library.NextFlag = function(self)
        self.UnnamedFlags = self.UnnamedFlags + 1
        return StringFormat("Flag Number %s", self.UnnamedFlags)
    end

    Library.SetFlag = function(self, Flag, Value)
        if Flag ~= nil then
            self.Flags[Flag] = Value
        end
    end

    local function EncodeValue(Value)
        local Kind = typeof(Value)
        if Kind == "Color3" then
            return { __t = "Color3", R = Value.R, G = Value.G, B = Value.B }
        elseif Kind == "EnumItem" then
            return { __t = "Enum", E = tostring(Value.EnumType), N = Value.Name }
        elseif Kind == "table" then
            local Out = { }
            for Key, Inner in Value do
                Out[Key] = EncodeValue(Inner)
            end
            return Out
        else
            return Value
        end
    end

    local function DecodeValue(Value)
        if type(Value) == "table" then
            if Value.__t == "Color3" then
                return ColorNew(Value.R, Value.G, Value.B)
            elseif Value.__t == "Enum" then
                local Ok, Result = pcall(function() return Enum[Value.E][Value.N] end)
                return Ok and Result or nil
            else
                local Out = { }
                for Key, Inner in Value do
                    Out[Key] = DecodeValue(Inner)
                end
                return Out
            end
        else
            return Value
        end
    end

    Library.ConfigFolder = function(self)
        if isfolder then
            if not isfolder(Library.Directory) then makefolder(Library.Directory) end
            if not isfolder(Library.Directory .. "/configs") then makefolder(Library.Directory .. "/configs") end
        end
        return Library.Directory .. "/configs"
    end

    Library.Serialize = function(self)
        local Data = { }
        for Flag, Value in self.Flags do
            Data[Flag] = EncodeValue(Value)
        end
        return HttpService:JSONEncode(Data)
    end

    Library.Apply = function(self, Text)
        local Ok, Data = pcall(function() return HttpService:JSONDecode(Text) end)
        if not Ok or type(Data) ~= "table" then return false end
        for Flag, Value in Data do
            local Decoded = DecodeValue(Value)
            local Setter = self.SetFlags[Flag]
            if Setter then
                pcall(Setter, Decoded)
            else
                self.Flags[Flag] = Decoded
            end
        end
        return true
    end

    Library.SaveConfig = function(self, Name)
        if not Name or Name == "" or not writefile then return false end
        writefile(self:ConfigFolder() .. "/" .. Name .. ".cfg", self:Serialize())
        return true
    end

    Library.LoadConfig = function(self, Name)
        if not Name or Name == "" or not isfile then return false end
        local Path = self:ConfigFolder() .. "/" .. Name .. ".cfg"
        if not isfile(Path) then return false end
        return self:Apply(readfile(Path))
    end

    Library.DeleteConfig = function(self, Name)
        local Path = self:ConfigFolder() .. "/" .. (Name or "") .. ".cfg"
        if isfile and isfile(Path) and delfile then
            delfile(Path)
            return true
        end
        return false
    end

    Library.RenameConfig = function(self, Old, New)
        if not Old or not New or New == "" then return false end
        local Folder = self:ConfigFolder()
        local OldPath = Folder .. "/" .. Old .. ".cfg"
        if not (isfile and isfile(OldPath) and writefile) then return false end
        writefile(Folder .. "/" .. New .. ".cfg", readfile(OldPath))
        if delfile then delfile(OldPath) end
        return true
    end

    Library.ListConfigs = function(self)
        local Out = { }
        if not listfiles then return Out end
        local Ok, Files = pcall(listfiles, self:ConfigFolder())
        if not Ok then return Out end
        for Index, Path in Files do
            local Name = string.match(Path, "([^/\\]+)%.cfg$")
            if Name then
                TableInsert(Out, Name)
            end
        end
        return Out
    end

    Library.CopyConfig = function(self, Name)
        local Path = self:ConfigFolder() .. "/" .. (Name or "") .. ".cfg"
        if isfile and isfile(Path) and setclipboard then
            setclipboard(readfile(Path))
            return true
        end
        return false
    end

    Library.PasteConfig = function(self, Name)
        if not Name or Name == "" or not writefile then return false end
        local Reader = getclipboard or readclipboard
        if not Reader then return false end
        local Ok, Text = pcall(Reader)
        if not Ok or type(Text) ~= "string" or Text == "" then return false end
        if not pcall(function() return HttpService:JSONDecode(Text) end) then return false end
        writefile(self:ConfigFolder() .. "/" .. Name .. ".cfg", Text)
        return true
    end

    Library.SetAutoload = function(self, Name)
        if not writefile then return false end
        writefile(self:ConfigFolder() .. "/autoload.txt", Name or "")
        return true
    end

    Library.GetAutoload = function(self)
        local Path = self:ConfigFolder() .. "/autoload.txt"
        if isfile and isfile(Path) then
            return readfile(Path)
        end
        return nil
    end

    Library.LoadAutoload = function(self)
        local Name = self:GetAutoload()
        if Name and Name ~= "" then
            return self:LoadConfig(Name)
        end
        return false
    end

    Library.Unload = function(self)
        for Index, Value in self.Connections do
            Value.Connection:Disconnect()
        end

        for Index, Value in self.Threads do
            coroutine.close(Value)
        end

        if self.ScreenGui then
            self.ScreenGui:Destroy()
        end

        Library = nil
        if getgenv then
            getgenv().Library = nil
        end
    end

    local Instances = { } do
        Instances.__index = Instances

        Instances.Create = function(self, Class, Properties)
            local NewItem = {
                Instance = InstanceNew(Class),
                Properties = Properties,
                Class = Class
            }

            setmetatable(NewItem, Instances)

            for Property, Value in NewItem.Properties do
                NewItem.Instance[Property] = Value
            end

            return NewItem
        end

        Instances.Connect = function(self, Event, Callback, Name)
            if not self.Instance then
                return
            end

            return Library:Connect(self.Instance[Event], Callback, Name)
        end

        Instances.OnHover = function(self, Function)
            if not self.Instance then
                return
            end

            return Library:Connect(self.Instance.MouseEnter, Function)
        end

        Instances.OnHoverLeave = function(self, Function)
            if not self.Instance then
                return
            end

            return Library:Connect(self.Instance.MouseLeave, Function)
        end

        Instances.Clean = function(self)
            if not self.Instance then
                return
            end

            self.Instance:Destroy()
            self = nil
        end
    end

    local CustomFont = { } do
        function CustomFont:New(Name, Weight, Style, Data)
            if isfolder and not isfolder(Library.Directory) then
                makefolder(Library.Directory)
            end

            local FontTtf = Library.Directory .. "/" .. Data.Id .. ".ttf"

            if not isfile(FontTtf) then
                writefile(FontTtf, game:HttpGet(Data.Url))
            end

            local FontData = {
                name = Name,
                faces = { {
                    name = Name,
                    weight = Weight,
                    style = Style,
                    assetId = getcustomasset(FontTtf)
                } }
            }

            local FontFile = Library.Directory .. "/" .. Name .. ".font"

            writefile(FontFile, HttpService:JSONEncode(FontData))
            return Font.new(getcustomasset(FontFile))
        end

        Library.Font = CustomFont:New("WindowsXPTahoma", 400, "Normal", {
            Id = "windows-xp-tahoma",
            Url = "https://github.com/sametexe001/luas/raw/refs/heads/main/fonts/windows-xp-tahoma.ttf"
        })
    end

    local BoundsParams = InstanceNew("GetTextBoundsParams")
    BoundsParams.Width = math.huge

    Library.Measure = function(self, Text, Size)
        BoundsParams.Text = Text
        BoundsParams.Font = Library.Font
        BoundsParams.Size = Size or Sizes.Tab
        return TextService:GetTextBoundsAsync(BoundsParams).X
    end

    Library.NextZ = function(self)
        self.Z = self.Z + 1
        return self.Z
    end

    Library.Rect = function(self, Parent, X, Y, W, H, Color, Z)
        return Instances:Create("Frame", {
            Name = "\0",
            BorderSizePixel = 0,
            BackgroundColor3 = Color,
            Position = UDim2Offset(X, Y),
            Size = UDim2Offset(W, H),
            ZIndex = Z or self:NextZ(),
            Parent = Parent
        }).Instance
    end

    Library.Outline = function(self, Parent, X, Y, W, H, Color, Z)
        self:Rect(Parent, X, Y, W, 1, Color, Z)
        self:Rect(Parent, X, Y + H - 1, W, 1, Color, Z)
        self:Rect(Parent, X, Y, 1, H, Color, Z)
        self:Rect(Parent, X + W - 1, Y, 1, H, Color, Z)
    end

    Library.Glyph = function(self, Parent, Size, X, Y, W, H, Text, Color, XAlign, YAlign, Anchor, Z)
        return Instances:Create("TextLabel", {
            Name = "\0",
            BackgroundTransparency = 1,
            FontFace = Library.Font,
            TextSize = Size,
            Text = Text,
            TextColor3 = Color,
            TextXAlignment = XAlign or Enum.TextXAlignment.Left,
            TextYAlignment = YAlign or Enum.TextYAlignment.Center,
            AnchorPoint = Anchor or Vector2New(0, 0),
            Position = UDim2Offset(X, Y),
            Size = UDim2Offset(W, H),
            ZIndex = Z or self:NextZ(),
            Parent = Parent
        }).Instance
    end

    Library.AddShimmer = function(self, Parent, W, H, FadeObject, IsTab, Z)
        local Segments = MathClamp(MathFloor(W), 1, 64)

        for Index = 0, Segments - 1 do
            local X0 = MathFloor(W * Index / Segments)
            local X1 = MathFloor(W * (Index + 1) / Segments)

            local Frame = self:Rect(Parent, X0, 0, MathMax(1, X1 - X0), H, Theme.Glint, Z)
            Frame.BackgroundTransparency = 1

            TableInsert(self.Shimmers, {
                Frame = Frame,
                Scalar = (Index + 0.5) / Segments,
                Fade = FadeObject,
                IsTab = IsTab
            })
        end
    end

    Library.Checker = function(self, Parent, X, Y, W, H, Z)
        for OffsetY = 0, H - 1, 4 do
            for OffsetX = 0, W - 1, 4 do
                local Dark = (MathFloor(OffsetX / 4) + MathFloor(OffsetY / 4)) % 2 == 1
                self:Rect(Parent, X + OffsetX, Y + OffsetY, MathMin(4, W - OffsetX), MathMin(4, H - OffsetY),
                    Dark and FromRGB(80, 80, 80) or FromRGB(120, 120, 120), Z)
            end
        end
    end

    Library.RegisterDrag = function(self, Area, OnMove)
        local Drag = {
            Dragging = false,
            Area = Area,
            Move = OnMove
        }

        Area.MouseButton1Down:Connect(function()
            Drag.Dragging = true
        end)

        TableInsert(self.Drags, Drag)
        return Drag
    end

    Library.CharWidth = function(self, Character)
        return self:Measure(Character, Sizes.Title)
    end

    Library.Bold = function(self, Parent, X, Text, Color)
        local CursorX = X
        local First = true

        for Index = 1, #Text do
            local Character = Text:sub(Index, Index)

            if Character == " " then
                CursorX = CursorX + self:CharWidth(" ")
            else
                if not First then
                    CursorX = CursorX + Sizes.TitleSpacing
                end

                First = false

                self:Glyph(Parent, Sizes.Title, CursorX + 1, 8, self:CharWidth(Character) + 2, 16, Character, Theme.Outer)
                local MainOne = self:Glyph(Parent, Sizes.Title, CursorX, 7, self:CharWidth(Character) + 2, 16, Character, Color)
                local MainTwo = self:Glyph(Parent, Sizes.Title, CursorX + 1, 7, self:CharWidth(Character) + 2, 16, Character, Color)

                if Color == Theme.Accent then
                    TableInsert(self.AccentTexts, MainOne)
                    TableInsert(self.AccentTexts, MainTwo)
                end

                CursorX = CursorX + self:CharWidth(Character)
            end
        end

        return CursorX
    end

    Library.DrawTitle = function(self)
        for Index, Child in self.TitleHolder:GetChildren() do
            Child:Destroy()
        end

        local Kept = { }
        for Index, Object in self.AccentTexts do
            if Object.Parent then
                TableInsert(Kept, Object)
            end
        end
        self.AccentTexts = Kept

        local CursorX = 8
        CursorX = self:Bold(self.TitleHolder, CursorX, self.TitlePre, Theme.Text) + 2
        CursorX = self:Bold(self.TitleHolder, CursorX, self.TitleAccent, Theme.Accent) + 2
        self:Bold(self.TitleHolder, CursorX, self.TitlePost, Theme.Text)
    end

    Library.OpenPopupFor = function(self, Owner)
        if self.OpenPopup and self.OpenPopup ~= Owner then
            self.OpenPopup.Popup.Visible = false
        end

        Owner.Popup.Visible = true
        self.OpenPopup = Owner
        self.Blocker.Visible = true
    end

    Library.RegisterScrollPopup = function(self, Popup, X, Y, Tab)
        TableInsert(self.ScrollPopups, { Popup = Popup, X = X, Y = Y, Tab = Tab })
    end

    Library.SetAccent = function(self, Color)
        Theme.Accent = Color
        local Dark = self:Multiply(Color, 0.535)

        for Index, Object in self.AccentSolids do
            Object.BackgroundColor3 = Color
        end

        for Index, Object in self.AccentTexts do
            Object.TextColor3 = Color
        end

        for Index, Gradient in self.AccentGrads do
            Gradient.Color = RGBSequence(Color, Dark)
        end

        for Index, Tab in self.Tabs do
            Tab.Indicator.BackgroundColor3 = Color
        end

        for Index, Dropdown in self.Dropdowns do
            for OptionIndex, Row in Dropdown.Rows do
                Row.Main.TextColor3 = (Row.Index + 1 == Dropdown.Selected) and Color or Theme.Text
            end
        end
    end

    Library.MakePicker = function(self, Content, X1, RowY, PopupX, PopupY, Default, Alpha, Callback, Flag, Tab)
        local Frame = self.Gui

        local Hue, Saturation, Value = Color3.toHSV(Default)
        local AlphaValue = Alpha or 1

        self:Checker(Content, X1, RowY, 20, 10)
        local Overlay = self:Rect(Content, X1, RowY, 20, 10, Theme.Glint)
        local OverlayGradient = Instances:Create("UIGradient", {
            Name = "\0",
            Rotation = 90,
            Parent = Overlay
        }).Instance
        self:Outline(Content, X1, RowY, 20, 10, Theme.Outer)

        local PopupZ = 9001
        local Popup = self:Rect(Frame, PopupX, PopupY, 128, 126, Theme.Fill, PopupZ)
        Popup.Visible = false
        if Tab then
            self:RegisterScrollPopup(Popup, PopupX, PopupY, Tab)
        end
        self:Outline(Popup, 1, 1, 126, 124, Theme.Inner, PopupZ + 1)
        self:Outline(Popup, 0, 0, 128, 126, Theme.Outer, PopupZ + 1)

        local SatValue = self:Rect(Popup, 6, 6, 100, 100, Theme.Glint, PopupZ + 2)
        local SatValueGradient = Instances:Create("UIGradient", { Name = "\0", Rotation = 0, Parent = SatValue }).Instance
        local ValueLayer = self:Rect(Popup, 6, 6, 100, 100, ColorNew(0, 0, 0), PopupZ + 2)
        local ValueGradient = Instances:Create("UIGradient", {
            Name = "\0",
            Rotation = 90,
            Transparency = NumSequence({ NumSequenceKeypoint(0, 1), NumSequenceKeypoint(1, 0) }),
            Parent = ValueLayer
        }).Instance
        self:Outline(Popup, 7, 7, 98, 98, Theme.Inner, PopupZ + 3)
        self:Outline(Popup, 6, 6, 100, 100, Theme.Outer, PopupZ + 3)

        local HueBar = self:Rect(Popup, 110, 6, 12, 100, Theme.Glint, PopupZ + 2)
        local HueKeypoints = { }
        for Index = 0, 6 do
            TableInsert(HueKeypoints, RGBSequenceKeypoint(Index / 6, FromHSV(Index / 6, 1, 1)))
        end
        Instances:Create("UIGradient", { Name = "\0", Rotation = 90, Color = RGBSequence(HueKeypoints), Parent = HueBar })
        self:Outline(Popup, 111, 7, 10, 98, Theme.Inner, PopupZ + 3)
        self:Outline(Popup, 110, 6, 12, 100, Theme.Outer, PopupZ + 3)

        self:Checker(Popup, 6, 110, 100, 10, PopupZ + 2)
        local AlphaBar = self:Rect(Popup, 6, 110, 100, 10, Theme.Glint, PopupZ + 2)
        local AlphaGradient = Instances:Create("UIGradient", {
            Name = "\0",
            Rotation = 0,
            Transparency = NumSequence({ NumSequenceKeypoint(0, 1), NumSequenceKeypoint(1, 0) }),
            Parent = AlphaBar
        }).Instance
        self:Outline(Popup, 7, 111, 98, 8, Theme.Inner, PopupZ + 3)
        self:Outline(Popup, 6, 110, 100, 10, Theme.Outer, PopupZ + 3)

        local SatCursor = Instances:Create("Frame", {
            Name = "\0",
            AnchorPoint = Vector2New(0.5, 0.5),
            Size = UDim2Offset(8, 8),
            BackgroundTransparency = 1,
            BorderSizePixel = 0,
            ZIndex = PopupZ + 4,
            Parent = Popup
        }).Instance
        Instances:Create("UICorner", { Name = "\0", CornerRadius = UDimNew(1, 0), Parent = SatCursor })
        Instances:Create("UIStroke", { Name = "\0", Color = ColorNew(0, 0, 0), Thickness = 1, Parent = SatCursor })
        local SatCursorInner = Instances:Create("Frame", {
            Name = "\0",
            AnchorPoint = Vector2New(0.5, 0.5),
            Position = UDim2New(0.5, 0, 0.5, 0),
            Size = UDim2Offset(6, 6),
            BackgroundTransparency = 1,
            BorderSizePixel = 0,
            ZIndex = PopupZ + 4,
            Parent = SatCursor
        }).Instance
        Instances:Create("UICorner", { Name = "\0", CornerRadius = UDimNew(1, 0), Parent = SatCursorInner })
        Instances:Create("UIStroke", { Name = "\0", Color = ColorNew(1, 1, 1), Thickness = 1, Parent = SatCursorInner })

        local HueCursorBorder = self:Rect(Popup, 0, 0, 16, 4, ColorNew(0, 0, 0), PopupZ + 4)
        local HueCursorWhite = self:Rect(Popup, 0, 0, 14, 2, ColorNew(1, 1, 1), PopupZ + 4)
        local AlphaCursorBorder = self:Rect(Popup, 0, 0, 4, 14, ColorNew(0, 0, 0), PopupZ + 4)
        local AlphaCursorWhite = self:Rect(Popup, 0, 0, 2, 12, ColorNew(1, 1, 1), PopupZ + 4)

        local function Refresh()
            local Rgb = FromHSV(Hue, Saturation, Value)
            OverlayGradient.Color = RGBSequence(Rgb, self:Multiply(Rgb, 0.55))
            Overlay.BackgroundTransparency = 1 - AlphaValue
            SatValueGradient.Color = RGBSequence(ColorNew(1, 1, 1), FromHSV(Hue, 1, 1))
            SatCursor.Position = UDim2Offset(6 + MathFloor(Saturation * 100), 6 + MathFloor((1 - Value) * 100))
            local HueY = 6 + Hue * 100
            HueCursorBorder.Position = UDim2Offset(108, MathFloor(HueY) - 2)
            HueCursorWhite.Position = UDim2Offset(109, MathFloor(HueY) - 1)
            AlphaGradient.Color = RGBSequence(Rgb)
            local AlphaX = 6 + AlphaValue * 100
            AlphaCursorBorder.Position = UDim2Offset(MathFloor(AlphaX) - 2, 108)
            AlphaCursorWhite.Position = UDim2Offset(MathFloor(AlphaX) - 1, 109)
            Library:SetFlag(Flag, Rgb)
            Library:SafeCall(Callback, Rgb, AlphaValue)
        end
        Refresh()

        local function MakeHit(X, Y, W, H)
            return Instances:Create("TextButton", {
                Name = "\0",
                BackgroundTransparency = 1,
                AutoButtonColor = false,
                Active = true,
                Text = "",
                Position = UDim2Offset(X, Y),
                Size = UDim2Offset(W, H),
                ZIndex = PopupZ + 5,
                Parent = Popup
            }).Instance
        end

        self:RegisterDrag(MakeHit(6, 6, 100, 100), function(TX, TY) Saturation = TX; Value = 1 - TY; Refresh() end)
        self:RegisterDrag(MakeHit(110, 6, 12, 100), function(TX, TY) Hue = TY; Refresh() end)
        self:RegisterDrag(MakeHit(6, 110, 100, 10), function(TX, TY) AlphaValue = TX; Refresh() end)

        local Wrapper = { Popup = Popup }

        function Wrapper:Set(Color, NewAlpha)
            Hue, Saturation, Value = Color3.toHSV(Color)
            AlphaValue = NewAlpha or AlphaValue
            Refresh()
        end

        local SwatchHit = Instances:Create("TextButton", {
            Name = "\0",
            BackgroundTransparency = 1,
            AutoButtonColor = false,
            Active = true,
            Text = "",
            Position = UDim2Offset(X1, RowY),
            Size = UDim2Offset(20, 10),
            ZIndex = self:NextZ(),
            Parent = Content
        })

        SwatchHit:Connect("MouseButton1Click", function()
            if Popup.Visible then
                Popup.Visible = false
                self.Blocker.Visible = false
                self.OpenPopup = nil
            else
                self:OpenPopupFor(Wrapper)
            end
        end)

        if Flag then
            Library.SetFlags[Flag] = function(Color, NewAlpha)
                Wrapper:Set(Color, NewAlpha)
            end
        end

        return Wrapper
    end

    Library.LayoutTabs = function(self)
        local Kept = { }
        for Index, Entry in self.Shimmers do
            if not Entry.IsTab then
                TableInsert(Kept, Entry)
            end
        end
        self.Shimmers = Kept

        local Count = #self.Tabs
        local InnerLeft, InnerRight = 6, self.Width - 6
        local Span = InnerRight - InnerLeft

        for Number = 0, Count - 1 do
            local Tab = self.Tabs[Number + 1]

            if Tab.Holder then
                Tab.Holder:Destroy()
            end

            local TabMin = InnerLeft + MathFloor(Span * Number / Count)
            local TabMax = InnerLeft + MathFloor(Span * (Number + 1) / Count)
            local TabWidth = TabMax - TabMin

            local Holder = Instances:Create("Frame", {
                Name = "\0",
                BackgroundTransparency = 1,
                Active = true,
                Position = UDim2Offset(TabMin, 26),
                Size = UDim2Offset(TabWidth, 29),
                ZIndex = self:NextZ(),
                Parent = self.Frame
            }).Instance
            Tab.Holder = Holder

            Tab.FillFrame = self:Rect(Holder, 0, 2, TabWidth, 25, Theme.Dark)
            Tab.FillFrame.BackgroundTransparency = 1

            self:Glyph(Holder, Sizes.Tab, 1, 1, TabWidth, 29, Tab.Name, Theme.Outer, Enum.TextXAlignment.Center)
            Tab.Label = self:Glyph(Holder, Sizes.Tab, 0, 0, TabWidth, 29, Tab.Name, Theme.Text, Enum.TextXAlignment.Center)

            local IndicatorHalf = MathFloor(TabWidth * 0.365)
            local CenterX = MathFloor((TabMin + TabMax) * 0.5)
            Tab.Indicator = self:Rect(Holder, (CenterX - IndicatorHalf) - TabMin, 25, IndicatorHalf * 2, 2, Theme.Accent)
            Tab.Indicator.BackgroundTransparency = 1
            Tab.Indicator.ClipsDescendants = true
            Tab.Indicator.Visible = false
            self:AddShimmer(Tab.Indicator, IndicatorHalf * 2, 2, Tab, true)

            local SelectedIndex = Number + 1
            Library:Connect(Holder.InputBegan, function(Input)
                if Input.UserInputType == Enum.UserInputType.MouseButton1 then
                    self.Selected = SelectedIndex
                end
            end)
        end
    end

    Library.Tab = function(self, Data)
        Data = Data or { }

        local Width, Height = self.Width, self.Height

        local Content = Instances:Create("Frame", {
            Name = "\0",
            BackgroundTransparency = 1,
            Position = UDim2Offset(6, 56),
            Size = UDim2Offset(Width - 12, Height - 62),
            ClipsDescendants = true,
            Visible = false,
            ZIndex = self:NextZ(),
            Parent = self.Frame
        }).Instance

        local Scroller = Instances:Create("Frame", {
            Name = "\0",
            BackgroundTransparency = 1,
            Position = UDim2Offset(0, 0),
            Size = UDim2Offset(Width - 12, 4000),
            ZIndex = self:NextZ(),
            Parent = Content
        }).Instance

        local Tab = setmetatable({
            Window = self,
            Name = Data.Name or Data.name or "Tab",
            Fade = 0,
            Content = Content,
            Scroller = Scroller,
            Scroll = 0,
            ScrollTarget = 0,
            MaxScroll = 0,
            ColY = { [0] = 10, [1] = 10 }
        }, Library.Tabs)

        local Shade = self:Rect(Content, 0, Height - 84, Width - 12, 22, Theme.Outer, 6000)
        Shade.BackgroundTransparency = 1
        Shade.Visible = false
        Instances:Create("UIGradient", {
            Name = "\0",
            Rotation = 90,
            Transparency = NumSequence({ NumSequenceKeypoint(0, 1), NumSequenceKeypoint(1, 0) }),
            Parent = Shade
        })
        Tab.Shade = Shade

        local Thumb = self:Rect(Content, Width - 14, 1, 2, 12, Theme.Accent, 6000)
        Thumb.Visible = false
        Tab.Thumb = Thumb
        TableInsert(self.AccentSolids, Thumb)

        TableInsert(self.Tabs, Tab)
        self:LayoutTabs()
        return Tab
    end

    Library.Tabs.Section = function(self, Data)
        Data = Data or { }

        local Window = self.Window
        local Content = self.Scroller

        local Name = Data.Name or Data.name or "Section"
        local Column = Data.Side or Data.side or 0

        local SectionWidth = MathFloor((Window.Width - 30) * 0.5)
        local X = 6 + Column * (SectionWidth + 6)
        local Y = self.ColY[Column]

        local Items = { } do
            Items["Fill"] = Window:Rect(Content, X, Y, SectionWidth, 24, Theme.Section)
            Window:Rect(Content, X + 1, Y + 1, SectionWidth - 2, 1, Theme.Inner)
            Items["InnerBottom"] = Window:Rect(Content, X + 1, Y + 22, SectionWidth - 2, 1, Theme.Inner)
            Items["InnerLeft"] = Window:Rect(Content, X + 1, Y + 1, 1, 22, Theme.Inner)
            Items["InnerRight"] = Window:Rect(Content, X + SectionWidth - 2, Y + 1, 1, 22, Theme.Inner)
            Window:Rect(Content, X, Y, SectionWidth, 1, Theme.Outer)
            Items["OuterBottom"] = Window:Rect(Content, X, Y + 23, SectionWidth, 1, Theme.Outer)
            Items["OuterLeft"] = Window:Rect(Content, X, Y, 1, 24, Theme.Outer)
            Items["OuterRight"] = Window:Rect(Content, X + SectionWidth - 1, Y, 1, 24, Theme.Outer)

            Items["Accent"] = Window:Rect(Content, X + 2, Y + 2, SectionWidth - 4, 2, Theme.Accent)
            Items["Accent"].ClipsDescendants = true
            TableInsert(Window.AccentSolids, Items["Accent"])
            Window:AddShimmer(Items["Accent"], SectionWidth - 4, 2)

            local LabelWidth = Window:Measure(Name)
            Window:Rect(Content, X + 4, Y, 8 + LabelWidth, 4, Theme.Section)
            Window:Glyph(Content, Sizes.Tab, X + 9, Y - 6, LabelWidth + 4, 14, Name, Theme.Outer)
            Window:Glyph(Content, Sizes.Tab, X + 8, Y - 7, LabelWidth + 4, 14, Name, Theme.Text)
        end

        local Section = setmetatable({
            Window = Window,
            Content = Content,
            Tab = self,
            X = X,
            W = SectionWidth,
            Max = X + SectionWidth,
            Y = Y,
            NextY = Y + 11,
            Column = Column,
            ColY = self.ColY,
            Elements = Items
        }, Library.Sections)

        Section:Fit()
        return Section
    end

    Library.Sections.Fit = function(self)
        local Items = self.Elements
        local Height = MathMax(24, (self.NextY - self.Y) + 6)
        local X, Y, W = self.X, self.Y, self.W

        Items["Fill"].Size = UDim2Offset(W, Height)
        Items["InnerBottom"].Position = UDim2Offset(X + 1, Y + Height - 2)
        Items["InnerLeft"].Size = UDim2Offset(1, Height - 2)
        Items["InnerRight"].Size = UDim2Offset(1, Height - 2)
        Items["OuterBottom"].Position = UDim2Offset(X, Y + Height - 1)
        Items["OuterLeft"].Size = UDim2Offset(1, Height)
        Items["OuterRight"].Size = UDim2Offset(1, Height)
        self.ColY[self.Column] = Y + Height + 8
    end

    Library.Sections.Toggle = function(self, Data)
        Data = Data or { }

        local Window = self.Window
        local Content = self.Content

        local Toggle = setmetatable({
            Window = Window,
            Content = Content,
            Tab = self.Tab,
            X = self.X,
            Max = self.Max,
            RowY = self.NextY,
            CpIndex = 0,

            Name = Data.Name or Data.name or "Toggle",
            Flag = Data.Flag or Data.flag or (Data.Name or Data.name),
            Default = Data.Default or Data.default or false,
            Callback = Data.Callback or Data.callback,

            Value = false,
            Fade = 0,
            Class = "Toggle"
        }, Library.Toggles)

        local TX = self.X + 8
        local TY = self.NextY
        self.NextY = TY + 15

        local LabelWidth = Window:Measure(Toggle.Name)

        local Items = { } do
            Items["Dark"] = Window:Rect(Content, TX, TY, 10, 10, Theme.Glint)
            Instances:Create("UIGradient", {
                Name = "\0",
                Rotation = 90,
                Color = RGBSequence(FromRGB(13, 13, 13), FromRGB(10, 10, 10)),
                Parent = Items["Dark"]
            })

            Items["Accent"] = Window:Rect(Content, TX, TY, 10, 10, Theme.Glint)
            Items["Accent"].BackgroundTransparency = 1
            local AccentGradient = Instances:Create("UIGradient", {
                Name = "\0",
                Rotation = 90,
                Color = RGBSequence(FromRGB(190, 162, 227), FromRGB(101, 86, 121)),
                Parent = Items["Accent"]
            }).Instance
            TableInsert(Window.AccentGrads, AccentGradient)

            Window:Outline(Content, TX, TY, 10, 10, Theme.Outer)

            Window:Glyph(Content, Sizes.Tab, TX + 18, TY + 6, LabelWidth + 4, 14, Toggle.Name, Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
            Items["Label"] = Window:Glyph(Content, Sizes.Tab, TX + 17, TY + 5, LabelWidth + 4, 14, Toggle.Name, Theme.Dim,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))

            Items["Hit"] = Instances:Create("TextButton", {
                Name = "\0",
                BackgroundTransparency = 1,
                Text = "",
                AutoButtonColor = false,
                Active = true,
                Position = UDim2Offset(TX, TY),
                Size = UDim2Offset(17 + LabelWidth, 10),
                ZIndex = Window:NextZ(),
                Parent = Content
            })
        end

        Toggle.AccentLayer = Items["Accent"]
        Toggle.LabelText = Items["Label"]
        Toggle.Value = Toggle.Default and true or false
        Toggle.Fade = Toggle.Value and 1 or 0

        TableInsert(Window.Toggles, Toggle)
        Library:SetFlag(Toggle.Flag, Toggle.Value)
        Library:SafeCall(Toggle.Callback, Toggle.Value)

        Items["Hit"]:Connect("MouseButton1Click", function()
            Toggle:Set(not Toggle.Value)
        end)

        Library.SetFlags[Toggle.Flag] = function(Value)
            Toggle:Set(Value)
        end

        self:Fit()
        return Toggle
    end

    function Library.Toggles:Get()
        return self.Value
    end

    function Library.Toggles:Set(Value)
        self.Value = Value and true or false
        Library:SetFlag(self.Flag, self.Value)
        Library:SafeCall(self.Callback, self.Value)
    end

    function Library.Toggles:Keybind(Data)
        return Library:CreateKeybind(self, Data)
    end

    function Library.Toggles:Colorpicker(Data)
        return Library:CreateColorpicker(self, Data)
    end

    Library.CreateKeybind = function(self, Owner, Data)
        Data = Data or { }

        local Window = Owner.Window
        local Content = Owner.Content
        local Frame = Window.Gui
        local Flag = Data.Flag or Data.flag

        local Mode = Data.Mode or Data.mode
        local RightColumn = Owner.X + 164
        local CursorY = Owner.RowY + 5

        local Keybind = {
            Key = Data.Default or Data.default,
            Mode = Mode or 2,
            Listening = false,
            HasMode = (Mode ~= nil),
            Name = Owner.Name or "keybind",
            Active = ((Mode or 2) == 0),
            Class = "Keybind"
        }

        local Shadow = Window:Glyph(Content, Sizes.Tab, RightColumn + 1, CursorY + 1, 90, 14, "", Theme.Outer,
            Enum.TextXAlignment.Right, Enum.TextYAlignment.Center, Vector2New(1, 0.5))
        Shadow.AutomaticSize = Enum.AutomaticSize.X

        local Main = Instances:Create("TextButton", {
            Name = "\0",
            BackgroundTransparency = 1,
            AutoButtonColor = false,
            Active = true,
            FontFace = Library.Font,
            TextSize = Sizes.Tab,
            TextColor3 = Theme.Dim,
            TextXAlignment = Enum.TextXAlignment.Right,
            TextYAlignment = Enum.TextYAlignment.Center,
            AnchorPoint = Vector2New(1, 0.5),
            Position = UDim2Offset(RightColumn, CursorY),
            Size = UDim2Offset(0, 14),
            AutomaticSize = Enum.AutomaticSize.X,
            Text = "",
            ZIndex = Window:NextZ(),
            Parent = Content
        })

        local function Update()
            local Name = Keybind.Listening and "..." or Library:KeyName(Keybind.Key)
            local Buffer = "[" .. Name .. "]"
            Main.Instance.Text = Buffer
            Shadow.Text = Buffer
            Library:SetFlag(Flag, Keybind.Key)
        end
        Update()

        local function RefreshList()
            if Keybind.ListItem then
                Keybind.ListItem:Update()
                Keybind.ListItem:SetActive(Keybind.Active)
            end
            if Window.KeyList then
                Window.KeyList:Refresh()
            end
        end

        local function Matches(Input)
            local Key = Keybind.Key
            if Key == nil then
                return false
            end
            if Input.UserInputType == Enum.UserInputType.Keyboard then
                return Input.KeyCode == Key
            elseif Key == "m1" then
                return Input.UserInputType == Enum.UserInputType.MouseButton1
            elseif Key == "m2" then
                return Input.UserInputType == Enum.UserInputType.MouseButton2
            elseif Key == "m3" then
                return Input.UserInputType == Enum.UserInputType.MouseButton3
            end
            return false
        end

        Main:Connect("MouseButton1Click", function()
            if Keybind.Listening then
                return
            end

            Keybind.Listening = true
            Update()

            local Connection
            Connection = UserInputService.InputBegan:Connect(function(Input)
                local Got = true

                if Input.UserInputType == Enum.UserInputType.Keyboard then
                    if Input.KeyCode == Enum.KeyCode.Escape then
                        Keybind.Key = nil
                    else
                        Keybind.Key = Input.KeyCode
                    end
                elseif Input.UserInputType == Enum.UserInputType.MouseButton1 then
                    Keybind.Key = "m1"
                elseif Input.UserInputType == Enum.UserInputType.MouseButton2 then
                    Keybind.Key = "m2"
                elseif Input.UserInputType == Enum.UserInputType.MouseButton3 then
                    Keybind.Key = "m3"
                else
                    Got = false
                end

                if Got then
                    Keybind.Listening = false
                    Update()
                    RefreshList()
                    Connection:Disconnect()
                end
            end)
        end)

        if Keybind.HasMode then
            local PopupZ = 9001
            local PopupX = (Owner.X + 170) - 58
            local PopupY = Owner.RowY + 68

            local Popup = Window:Rect(Frame, PopupX, PopupY, 58, 50, Theme.Fill, PopupZ)
            Popup.Visible = false
            Window:Outline(Popup, 0, 0, 58, 50, Theme.Outer, PopupZ + 1)
            Keybind.Popup = Popup
            Window:RegisterScrollPopup(Popup, PopupX, PopupY, Owner.Tab)
            Keybind.Rows = { }

            local Modes = { "always", "hold", "toggle" }
            for Index = 0, 2 do
                local RowY = 1 + Index * 16
                local Highlight = Window:Rect(Popup, 1, RowY, 56, 16, Theme.Hover, PopupZ + 2)
                Highlight.BackgroundTransparency = 1
                local RowShadow = Window:Glyph(Popup, Sizes.Tab, 6, RowY, 50, 16, Modes[Index + 1], Theme.Outer,
                    Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, nil, PopupZ + 3)

                local Button = Instances:Create("TextButton", {
                    Name = "\0",
                    BackgroundTransparency = 1,
                    AutoButtonColor = false,
                    Active = true,
                    FontFace = Library.Font,
                    TextSize = Sizes.Tab,
                    TextColor3 = (Keybind.Mode == Index) and Theme.Text or FromRGB(120, 120, 120),
                    TextXAlignment = Enum.TextXAlignment.Left,
                    TextYAlignment = Enum.TextYAlignment.Center,
                    Position = UDim2Offset(5, RowY),
                    Size = UDim2Offset(52, 16),
                    Text = Modes[Index + 1],
                    ZIndex = PopupZ + 4,
                    Parent = Popup
                })

                local Row = {
                    Highlight = Highlight,
                    Main = Button.Instance,
                    Shadow = RowShadow,
                    Index = Index,
                    RowH = 16,
                    Hovered = false,
                    HP = 0
                }
                TableInsert(Keybind.Rows, Row)
                TableInsert(Window.KeyRows, Row)

                Button:Connect("MouseEnter", function() Row.Hovered = true end)
                Button:Connect("MouseLeave", function() Row.Hovered = false end)
                Button:Connect("MouseButton1Click", function()
                    Keybind.Mode = Index
                    Keybind.Active = (Index == 0)
                    for RowIndex, Other in Keybind.Rows do
                        Other.Main.TextColor3 = (Keybind.Mode == Other.Index) and Theme.Text or FromRGB(120, 120, 120)
                    end
                    RefreshList()
                    Popup.Visible = false
                    Window.Blocker.Visible = false
                    Window.OpenPopup = nil
                end)
            end

            Main:Connect("MouseButton2Click", function()
                Window:OpenPopupFor(Keybind)
            end)
        end

        if Flag then
            Library.SetFlags[Flag] = function(Value)
                Keybind.Key = Value
                Update()
                RefreshList()
            end
        end

        Library:Connect(UserInputService.InputBegan, function(Input)
            if Keybind.Listening or not Matches(Input) then
                return
            end

            if Keybind.Mode == 2 then
                Keybind.Active = not Keybind.Active
            elseif Keybind.Mode == 1 then
                Keybind.Active = true
            end

            if Keybind.ListItem then
                Keybind.ListItem:SetActive(Keybind.Active)
            end
        end)

        Library:Connect(UserInputService.InputEnded, function(Input)
            if Keybind.Mode == 1 and Matches(Input) then
                Keybind.Active = false
                if Keybind.ListItem then
                    Keybind.ListItem:SetActive(Keybind.Active)
                end
            end
        end)

        if Keybind.HasMode then
            TableInsert(Window.Keybinds, Keybind)
            if Window.KeyList then
                Keybind.ListItem = Window.KeyList:Add(Keybind)
            end
        end

        return Keybind
    end

    Library.CreateColorpicker = function(self, Owner, Data)
        Data = Data or { }

        local Window = Owner.Window
        local Index = Owner.CpIndex or 0
        Owner.CpIndex = Index + 1

        local Default = Data.Default or Data.default or ColorNew(1, 1, 1)
        local Alpha = Data.Alpha or Data.alpha or 1
        local Flag = Data.Flag or Data.flag
        local Callback = Data.Callback or Data.callback

        local X1 = (Owner.Max - 6 - Index * 21) - 20
        Window:MakePicker(Owner.Content, X1, Owner.RowY, Owner.Max - 128, Owner.RowY + 68, Default, Alpha, Callback, Flag, Owner.Tab)
        return Owner
    end

    Library.Sections.Slider = function(self, Data)
        Data = Data or { }

        local Window = self.Window
        local Content = self.Content

        local Name = Data.Name or Data.name or "Slider"
        local Flag = Data.Flag or Data.flag or Name
        local Min = Data.Min or Data.min or 0
        local Max = Data.Max or Data.max or 100
        local Callback = Data.Callback or Data.callback

        local C = self.NextY
        local BarX = self.X + 8
        local BarWidth = self.W - 17
        local BarY = C + 11
        local BarHeight = 11
        self.NextY = BarY + 16

        local Slider = {
            Value = Data.Default or Data.default or Min,
            Min = Min,
            Max = Max,
            Flag = Flag,
            Class = "Slider"
        }

        local Items = { } do
            Window:Glyph(Content, Sizes.Tab, BarX + 2, C + 5, BarWidth, 14, Name, Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
            Window:Glyph(Content, Sizes.Tab, BarX + 1, C + 4, BarWidth, 14, Name, Theme.Text,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))

            Items["Bar"] = Window:Rect(Content, BarX, BarY, BarWidth, BarHeight, Theme.Glint)
            Instances:Create("UIGradient", {
                Name = "\0",
                Rotation = 90,
                Color = RGBSequence(FromRGB(13, 13, 13), FromRGB(10, 10, 10)),
                Parent = Items["Bar"]
            })

            Items["Fill"] = Window:Rect(Content, BarX, BarY, 0, BarHeight, Theme.Glint)
            local FillGradient = Instances:Create("UIGradient", {
                Name = "\0",
                Rotation = 90,
                Color = RGBSequence(FromRGB(190, 162, 227), FromRGB(101, 86, 121)),
                Parent = Items["Fill"]
            }).Instance
            TableInsert(Window.AccentGrads, FillGradient)

            Window:Outline(Content, BarX, BarY, BarWidth, BarHeight, Theme.Outer)

            local Right = BarX + BarWidth
            Items["ValueShadow"] = Window:Glyph(Content, Sizes.Tab, Right + 1, C + 5, 50, 14, "", Theme.Outer,
                Enum.TextXAlignment.Right, Enum.TextYAlignment.Center, Vector2New(1, 0.5))
            Items["ValueMain"] = Window:Glyph(Content, Sizes.Tab, Right, C + 4, 50, 14, "", Theme.Dim,
                Enum.TextXAlignment.Right, Enum.TextYAlignment.Center, Vector2New(1, 0.5))

            Items["Hit"] = Instances:Create("TextButton", {
                Name = "\0",
                BackgroundTransparency = 1,
                AutoButtonColor = false,
                Active = true,
                Text = "",
                Position = UDim2Offset(BarX, BarY),
                Size = UDim2Offset(BarWidth, BarHeight),
                ZIndex = Window:NextZ(),
                Parent = Content
            })
        end

        Slider.Bar = Items["Bar"]
        Slider.Fill = Items["Fill"]

        Slider.Update = function()
            local Ratio = (Slider.Max > Slider.Min) and (Slider.Value - Slider.Min) / (Slider.Max - Slider.Min) or 0
            Ratio = MathClamp(Ratio, 0, 1)
            Slider.Fill.Size = UDim2Offset(MathFloor(BarWidth * Ratio), BarHeight)
            local Text = StringFormat("%.0f", Slider.Value)
            Items["ValueMain"].Text = Text
            Items["ValueShadow"].Text = Text
            Library:SetFlag(Flag, Slider.Value)
            Library:SafeCall(Callback, Slider.Value)
        end

        function Slider:Get()
            return Slider.Value
        end

        function Slider:Set(Value)
            Slider.Value = MathClamp(Value, Slider.Min, Slider.Max)
            Slider.Update()
        end

        Slider.Update()

        Items["Hit"]:Connect("MouseButton1Down", function()
            Slider.Dragging = true
        end)

        TableInsert(Window.Sliders, Slider)
        Library.SetFlags[Flag] = function(Value)
            Slider:Set(Value)
        end

        self:Fit()
        return Slider
    end

    Library.Sections.Label = function(self, Data)
        Data = Data or { }

        local Window = self.Window
        local Content = self.Content
        local Name = Data.Name or Data.name or "Label"

        local C = self.NextY
        self.NextY = C + 15

        Window:Glyph(Content, Sizes.Tab, self.X + 9, C + 6, self.W - 17, 14, Name, Theme.Outer,
            Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
        Window:Glyph(Content, Sizes.Tab, self.X + 8, C + 5, self.W - 17, 14, Name, Theme.Text,
            Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))

        local Label = setmetatable({
            Window = Window,
            Content = Content,
            Tab = self.Tab,
            X = self.X,
            W = self.W,
            Max = self.Max,
            RowY = C,
            CpIndex = 0,
            Class = "Label"
        }, Library.Labels)

        self:Fit()
        return Label
    end

    function Library.Labels:Colorpicker(Data)
        return Library:CreateColorpicker(self, Data)
    end

    function Library.Labels:Keybind(Data)
        return Library:CreateKeybind(self, Data)
    end

    Library.Sections.Textbox = function(self, Data)
        Data = Data or { }

        local Window = self.Window
        local Content = self.Content

        local Name = Data.Name or Data.name or "Textbox"
        local Flag = Data.Flag or Data.flag or Name
        local Default = Data.Default or Data.default or ""
        local Callback = Data.Callback or Data.callback

        local C = self.NextY
        local BoxX = self.X + 8
        local BoxWidth = self.W - 17
        local BoxY = C + 11
        local BoxHeight = 14
        self.NextY = BoxY + 19

        local Items = { } do
            Window:Glyph(Content, Sizes.Tab, BoxX + 2, C + 5, BoxWidth, 14, Name, Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
            Window:Glyph(Content, Sizes.Tab, BoxX + 1, C + 4, BoxWidth, 14, Name, Theme.Text,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))

            Items["Box"] = Window:Rect(Content, BoxX, BoxY, BoxWidth, BoxHeight, Theme.Glint)
            Instances:Create("UIGradient", {
                Name = "\0",
                Rotation = 90,
                Color = RGBSequence(FromRGB(13, 13, 13), FromRGB(10, 10, 10)),
                Parent = Items["Box"]
            })
            Window:Outline(Content, BoxX, BoxY, BoxWidth, BoxHeight, Theme.Outer)

            Items["Input"] = Instances:Create("TextBox", {
                Name = "\0",
                BackgroundTransparency = 1,
                BorderSizePixel = 0,
                FontFace = Library.Font,
                TextSize = Sizes.Tab,
                TextColor3 = Theme.Text,
                TextXAlignment = Enum.TextXAlignment.Left,
                TextYAlignment = Enum.TextYAlignment.Center,
                ClearTextOnFocus = false,
                ClipsDescendants = true,
                Text = Default,
                Position = UDim2Offset(BoxX + 4, BoxY),
                Size = UDim2Offset(BoxWidth - 8, BoxHeight),
                ZIndex = Window:NextZ(),
                Parent = Content
            })
        end

        local Textbox = {
            Value = Default,
            Flag = Flag,
            Class = "Textbox"
        }

        local Input = Items["Input"]

        function Textbox:Get()
            return Input.Instance.Text
        end

        function Textbox:Set(Value)
            Input.Instance.Text = Value
        end

        Library:SetFlag(Flag, Input.Instance.Text)

        Library:Connect(Input.Instance:GetPropertyChangedSignal("Text"), function()
            Textbox.Value = Input.Instance.Text
            Library:SetFlag(Flag, Textbox.Value)
            Library:SafeCall(Callback, Textbox.Value)
        end)

        Library.SetFlags[Flag] = function(Value)
            Textbox:Set(Value)
        end

        self:Fit()
        return Textbox
    end

    Library.Sections.Dropdown = function(self, Data)
        Data = Data or { }

        local Window = self.Window
        local Content = self.Content
        local Frame = Window.Gui

        local Name = Data.Name or Data.name or "Dropdown"
        local Flag = Data.Flag or Data.flag or Name
        local Items_ = Data.Items or Data.items or { "One", "Two", "Three" }
        local Callback = Data.Callback or Data.callback

        local C = self.NextY
        local BoxX = self.X + 8
        local BoxWidth = self.W - 17
        local BoxY = C + 13
        local BoxHeight = 14
        self.NextY = BoxY + 19

        local Dropdown = {
            Selected = Data.Default or Data.default or 1,
            Items = Items_,
            Flag = Flag,
            Rows = { },
            Class = "Dropdown"
        }
        TableInsert(Window.Dropdowns, Dropdown)
        Library:SetFlag(Flag, Items_[Dropdown.Selected])

        local Elements = { } do
            Window:Glyph(Content, Sizes.Tab, BoxX + 2, C + 5, BoxWidth, 14, Name, Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
            Window:Glyph(Content, Sizes.Tab, BoxX + 1, C + 4, BoxWidth, 14, Name, Theme.Text,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))

            Elements["Box"] = Window:Rect(Content, BoxX, BoxY, BoxWidth, BoxHeight, Theme.Glint)
            Instances:Create("UIGradient", {
                Name = "\0",
                Rotation = 90,
                Color = RGBSequence(FromRGB(13, 13, 13), FromRGB(10, 10, 10)),
                Parent = Elements["Box"]
            })
            Window:Outline(Content, BoxX, BoxY, BoxWidth, BoxHeight, Theme.Outer)

            Elements["SelectedShadow"] = Window:Glyph(Content, Sizes.Tab, BoxX + 6, BoxY + 8, BoxWidth - 16, 14, Items_[Dropdown.Selected], Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
            Elements["SelectedMain"] = Window:Glyph(Content, Sizes.Tab, BoxX + 5, BoxY + 7, BoxWidth - 16, 14, Items_[Dropdown.Selected], Theme.Text,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))

            Window:Rect(Content, BoxX + BoxWidth - 13, BoxY + 1, 12, 12, Theme.Hover)
            local SignX = BoxX + BoxWidth - 7
            local SignY = BoxY + 7
            Window:Rect(Content, SignX - 3, SignY, 7, 1, Theme.Text)
            Elements["VBar"] = Window:Rect(Content, SignX, SignY - 3, 1, 7, Theme.Text)
        end

        local PopupZ = 9001
        local PopupX = BoxX + 6
        local PopupY = C + 84
        local PopupHeight = #Items_ * 18 + 2
        local Popup = Window:Rect(Frame, PopupX, PopupY, BoxWidth, PopupHeight, Theme.Fill, PopupZ)
        Popup.Visible = false
        Window:Outline(Popup, 0, 0, BoxWidth, PopupHeight, Theme.Outer, PopupZ + 1)
        Dropdown.Popup = Popup
        Window:RegisterScrollPopup(Popup, PopupX, PopupY, self.Tab)

        Library:Connect(Popup:GetPropertyChangedSignal("Visible"), function()
            Elements["VBar"].Visible = not Popup.Visible
        end)

        function Dropdown:Set(Index)
            Dropdown.Selected = Index
            Elements["SelectedMain"].Text = Items_[Index]
            Elements["SelectedShadow"].Text = Items_[Index]
            Library:SetFlag(Flag, Items_[Index])
            for RowIndex, Row in Dropdown.Rows do
                Row.Main.TextColor3 = (Row.Index + 1 == Dropdown.Selected) and Theme.Accent or Theme.Text
            end
            Library:SafeCall(Callback, Items_[Index])
        end

        function Dropdown:Get()
            return Items_[Dropdown.Selected]
        end

        for Index = 1, #Items_ do
            local RowY = 1 + (Index - 1) * 18
            local Highlight = Window:Rect(Popup, 1, RowY, BoxWidth - 2, 18, Theme.Hover, PopupZ + 2)
            Highlight.BackgroundTransparency = 1
            local RowShadow = Window:Glyph(Popup, Sizes.Tab, 6, RowY, BoxWidth - 10, 18, Items_[Index], Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, nil, PopupZ + 3)

            local Button = Instances:Create("TextButton", {
                Name = "\0",
                BackgroundTransparency = 1,
                AutoButtonColor = false,
                Active = true,
                FontFace = Library.Font,
                TextSize = Sizes.Tab,
                TextColor3 = (Index == Dropdown.Selected) and Theme.Accent or Theme.Text,
                TextXAlignment = Enum.TextXAlignment.Left,
                TextYAlignment = Enum.TextYAlignment.Center,
                Position = UDim2Offset(5, RowY),
                Size = UDim2Offset(BoxWidth - 6, 18),
                Text = Items_[Index],
                ZIndex = PopupZ + 4,
                Parent = Popup
            })

            local Row = {
                Highlight = Highlight,
                Main = Button.Instance,
                Shadow = RowShadow,
                Index = Index - 1,
                RowH = 18,
                Hovered = false,
                HP = 0
            }
            TableInsert(Dropdown.Rows, Row)
            TableInsert(Window.KeyRows, Row)

            Button:Connect("MouseEnter", function() Row.Hovered = true end)
            Button:Connect("MouseLeave", function() Row.Hovered = false end)
            Button:Connect("MouseButton1Click", function()
                Dropdown:Set(Index)
                Popup.Visible = false
                Window.Blocker.Visible = false
                Window.OpenPopup = nil
            end)
        end

        local Hit = Instances:Create("TextButton", {
            Name = "\0",
            BackgroundTransparency = 1,
            AutoButtonColor = false,
            Active = true,
            Text = "",
            Position = UDim2Offset(BoxX, BoxY),
            Size = UDim2Offset(BoxWidth, BoxHeight),
            ZIndex = Window:NextZ(),
            Parent = Content
        })
        Hit:Connect("MouseButton1Click", function()
            if Popup.Visible then
                Popup.Visible = false
                Window.Blocker.Visible = false
                Window.OpenPopup = nil
            else
                Window:OpenPopupFor(Dropdown)
            end
        end)

        Library.SetFlags[Flag] = function(Value)
            for Index = 1, #Items_ do
                if Items_[Index] == Value then
                    Dropdown:Set(Index)
                    break
                end
            end
        end

        self:Fit()
        return Dropdown
    end

    Library.Sections.DropdownMulti = function(self, Data)
        Data = Data or { }

        local Window = self.Window
        local Content = self.Content
        local Frame = Window.Gui

        local Name = Data.Name or Data.name or "Dropdown"
        local Flag = Data.Flag or Data.flag or Name
        local Items_ = Data.Items or Data.items or { "One", "Two", "Three" }
        local Callback = Data.Callback or Data.callback

        local C = self.NextY
        local BoxX = self.X + 8
        local BoxWidth = self.W - 17
        local BoxY = C + 13
        local BoxHeight = 14
        self.NextY = BoxY + 19

        local Mask = { }
        for Index = 1, #Items_ do
            Mask[Index] = false
        end
        do
            local Default = Data.Default or Data.default
            if type(Default) == "table" then
                for Key, Value in Default do
                    if type(Value) == "boolean" then
                        Mask[Key] = Value
                    elseif type(Value) == "string" then
                        for Index = 1, #Items_ do
                            if Items_[Index] == Value then
                                Mask[Index] = true
                            end
                        end
                    end
                end
            end
        end

        local Dropdown = {
            Mask = Mask,
            Items = Items_,
            Flag = Flag,
            Rows = { },
            Class = "DropdownMulti"
        }

        local Elements = { } do
            Window:Glyph(Content, Sizes.Tab, BoxX + 2, C + 5, BoxWidth, 14, Name, Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
            Window:Glyph(Content, Sizes.Tab, BoxX + 1, C + 4, BoxWidth, 14, Name, Theme.Text,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))

            Elements["Box"] = Window:Rect(Content, BoxX, BoxY, BoxWidth, BoxHeight, Theme.Glint)
            Instances:Create("UIGradient", {
                Name = "\0",
                Rotation = 90,
                Color = RGBSequence(FromRGB(13, 13, 13), FromRGB(10, 10, 10)),
                Parent = Elements["Box"]
            })
            Window:Outline(Content, BoxX, BoxY, BoxWidth, BoxHeight, Theme.Outer)

            Elements["SelectedShadow"] = Window:Glyph(Content, Sizes.Tab, BoxX + 6, BoxY + 8, BoxWidth - 16, 14, "None", Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
            Elements["SelectedShadow"].TextTruncate = Enum.TextTruncate.AtEnd
            Elements["SelectedMain"] = Window:Glyph(Content, Sizes.Tab, BoxX + 5, BoxY + 7, BoxWidth - 16, 14, "None", Theme.Text,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
            Elements["SelectedMain"].TextTruncate = Enum.TextTruncate.AtEnd

            Window:Rect(Content, BoxX + BoxWidth - 13, BoxY + 1, 12, 12, Theme.Hover)
            local SignX = BoxX + BoxWidth - 7
            local SignY = BoxY + 7
            Window:Rect(Content, SignX - 3, SignY, 7, 1, Theme.Text)
            Elements["VBar"] = Window:Rect(Content, SignX, SignY - 3, 1, 7, Theme.Text)
        end

        local PopupZ = 9001
        local PopupX = BoxX + 6
        local PopupY = C + 84
        local PopupHeight = #Items_ * 18 + 2
        local Popup = Window:Rect(Frame, PopupX, PopupY, BoxWidth, PopupHeight, Theme.Fill, PopupZ)
        Popup.Visible = false
        Window:Outline(Popup, 0, 0, BoxWidth, PopupHeight, Theme.Outer, PopupZ + 1)
        Dropdown.Popup = Popup
        Window:RegisterScrollPopup(Popup, PopupX, PopupY, self.Tab)

        Library:Connect(Popup:GetPropertyChangedSignal("Visible"), function()
            Elements["VBar"].Visible = not Popup.Visible
        end)

        local function Refresh()
            local Selected = { }
            for Index = 1, #Items_ do
                if Mask[Index] then
                    TableInsert(Selected, Items_[Index])
                end
            end
            local Count = #Selected
            local Preview = (Count == 0) and "None" or table.concat(Selected, ", ")
            if Count > 0 and Window:Measure(Preview) > BoxWidth - 18 then
                Preview = Count .. " selected"
            end
            Elements["SelectedMain"].Text = Preview
            Elements["SelectedShadow"].Text = Preview
            Library:SetFlag(Flag, Selected)
            Library:SafeCall(Callback, Selected)
        end

        function Dropdown:Set(Values)
            for Index = 1, #Items_ do
                Mask[Index] = false
            end
            if type(Values) == "table" then
                for Key, Value in Values do
                    if type(Value) == "boolean" then
                        Mask[Key] = Value
                    elseif type(Value) == "string" then
                        for Index = 1, #Items_ do
                            if Items_[Index] == Value then
                                Mask[Index] = true
                            end
                        end
                    end
                end
            end
            Refresh()
        end

        function Dropdown:Get()
            local Selected = { }
            for Index = 1, #Items_ do
                if Mask[Index] then
                    TableInsert(Selected, Items_[Index])
                end
            end
            return Selected
        end

        for Index = 1, #Items_ do
            local RowY = 1 + (Index - 1) * 18
            local Highlight = Window:Rect(Popup, 1, RowY, BoxWidth - 2, 18, Theme.Hover, PopupZ + 2)
            Highlight.BackgroundTransparency = 1
            local RowShadow = Window:Glyph(Popup, Sizes.Tab, 6, RowY, BoxWidth - 10, 18, Items_[Index], Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, nil, PopupZ + 3)

            local Button = Instances:Create("TextButton", {
                Name = "\0",
                BackgroundTransparency = 1,
                AutoButtonColor = false,
                Active = true,
                FontFace = Library.Font,
                TextSize = Sizes.Tab,
                TextColor3 = Mask[Index] and Theme.Text or Theme.Dim,
                TextXAlignment = Enum.TextXAlignment.Left,
                TextYAlignment = Enum.TextYAlignment.Center,
                Position = UDim2Offset(5, RowY),
                Size = UDim2Offset(BoxWidth - 6, 18),
                Text = Items_[Index],
                ZIndex = PopupZ + 4,
                Parent = Popup
            })

            local Row = {
                Highlight = Highlight,
                Main = Button.Instance,
                Shadow = RowShadow,
                Index = Index - 1,
                RowH = 18,
                Hovered = false,
                HP = 0
            }
            TableInsert(Dropdown.Rows, Row)
            TableInsert(Window.KeyRows, Row)
            TableInsert(Window.MultiRows, {
                Main = Button.Instance,
                Mask = Mask,
                Index = Index,
                Fade = Mask[Index] and 1 or 0
            })

            Button:Connect("MouseEnter", function() Row.Hovered = true end)
            Button:Connect("MouseLeave", function() Row.Hovered = false end)
            Button:Connect("MouseButton1Click", function()
                Mask[Index] = not Mask[Index]
                Refresh()
            end)
        end

        local Hit = Instances:Create("TextButton", {
            Name = "\0",
            BackgroundTransparency = 1,
            AutoButtonColor = false,
            Active = true,
            Text = "",
            Position = UDim2Offset(BoxX, BoxY),
            Size = UDim2Offset(BoxWidth, BoxHeight),
            ZIndex = Window:NextZ(),
            Parent = Content
        })
        Hit:Connect("MouseButton1Click", function()
            if Popup.Visible then
                Popup.Visible = false
                Window.Blocker.Visible = false
                Window.OpenPopup = nil
            else
                Window:OpenPopupFor(Dropdown)
            end
        end)

        Library.SetFlags[Flag] = function(Value)
            Dropdown:Set(Value)
        end

        Refresh()
        self:Fit()
        return Dropdown
    end

    Library.Sections.Colorpicker = function(self, Data)
        Data = Data or { }

        local Window = self.Window
        local Content = self.Content

        local Name = Data.Name or Data.name or "Colorpicker"
        local Flag = Data.Flag or Data.flag or Name
        local Default = Data.Default or Data.default or ColorNew(1, 1, 1)
        local Callback = Data.Callback or Data.callback

        local C = self.NextY
        self.NextY = C + 15

        Window:Glyph(Content, Sizes.Tab, self.X + 9, C + 6, self.W - 17, 14, Name, Theme.Outer,
            Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
        Window:Glyph(Content, Sizes.Tab, self.X + 8, C + 5, self.W - 17, 14, Name, Theme.Text,
            Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))

        local Picker = Window:MakePicker(Content, self.Max - 26, C, self.Max - 128, C + 68, Default, 1, Callback, Flag, self.Tab)

        self:Fit()
        return Picker
    end

    Library.Sections.Button = function(self, Data)
        Data = Data or { }

        local Window = self.Window
        local Content = self.Content

        local Name = Data.Name or Data.name or "Button"
        local Callback = Data.Callback or Data.callback

        local C = self.NextY
        local BoxX = self.X + 8
        local BoxWidth = self.W - 17
        local BoxY = C
        local BoxHeight = 14
        self.NextY = BoxY + 19

        local Box = Window:Rect(Content, BoxX, BoxY, BoxWidth, BoxHeight, Theme.Glint)
        Instances:Create("UIGradient", {
            Name = "\0",
            Rotation = 90,
            Color = RGBSequence(FromRGB(13, 13, 13), FromRGB(10, 10, 10)),
            Parent = Box
        })
        Window:Outline(Content, BoxX, BoxY, BoxWidth, BoxHeight, Theme.Outer)

        local Highlight = Window:Rect(Content, BoxX + 1, BoxY + 1, BoxWidth - 2, BoxHeight - 2, Theme.Hover)
        Highlight.BackgroundTransparency = 1

        Window:Glyph(Content, Sizes.Tab, BoxX + 1, BoxY + 1, BoxWidth, BoxHeight, Name, Theme.Outer,
            Enum.TextXAlignment.Center, Enum.TextYAlignment.Center)
        Window:Glyph(Content, Sizes.Tab, BoxX, BoxY, BoxWidth, BoxHeight, Name, Theme.Text,
            Enum.TextXAlignment.Center, Enum.TextYAlignment.Center)

        local Hit = Instances:Create("TextButton", {
            Name = "\0",
            BackgroundTransparency = 1,
            AutoButtonColor = false,
            Active = true,
            Text = "",
            Position = UDim2Offset(BoxX, BoxY),
            Size = UDim2Offset(BoxWidth, BoxHeight),
            ZIndex = Window:NextZ(),
            Parent = Content
        })
        Hit:Connect("MouseEnter", function() Highlight.BackgroundTransparency = 0.4 end)
        Hit:Connect("MouseLeave", function() Highlight.BackgroundTransparency = 1 end)
        Hit:Connect("MouseButton1Down", function() Highlight.BackgroundTransparency = 0 end)
        Hit:Connect("MouseButton1Up", function() Highlight.BackgroundTransparency = 0.4 end)
        Hit:Connect("MouseButton1Click", function()
            Library:SafeCall(Callback)
        end)

        self:Fit()
        return { Instance = Hit.Instance }
    end

    Library.Sections.SelectionBox = function(self, Data)
        Data = Data or { }

        local Window = self.Window
        local Content = self.Content

        local Name = Data.Name or Data.name
        local Items_ = Data.Items or Data.items or { }
        local Callback = Data.Callback or Data.callback
        local Flag = Data.Flag or Data.flag
        local VisibleRows = Data.Rows or Data.rows or 5
        local RowH = 16

        local C = self.NextY
        local BoxX = self.X + 8
        local BoxWidth = self.W - 17
        local BoxY = C

        if Name then
            Window:Glyph(Content, Sizes.Tab, BoxX + 2, C + 5, BoxWidth, 14, Name, Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
            Window:Glyph(Content, Sizes.Tab, BoxX + 1, C + 4, BoxWidth, 14, Name, Theme.Text,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5))
            BoxY = C + 13
        end

        local BoxHeight = VisibleRows * RowH + 2
        self.NextY = BoxY + BoxHeight + 5

        Window:Rect(Content, BoxX, BoxY, BoxWidth, BoxHeight, Theme.Fill)
        Window:Outline(Content, BoxX + 1, BoxY + 1, BoxWidth - 2, BoxHeight - 2, Theme.Inner)
        Window:Outline(Content, BoxX, BoxY, BoxWidth, BoxHeight, Theme.Outer)

        local Clip = Instances:Create("Frame", {
            Name = "\0",
            BackgroundTransparency = 1,
            ClipsDescendants = true,
            Position = UDim2Offset(BoxX + 1, BoxY + 1),
            Size = UDim2Offset(BoxWidth - 2, BoxHeight - 2),
            ZIndex = Window:NextZ(),
            Parent = Content
        }).Instance

        local Inner = Instances:Create("Frame", {
            Name = "\0",
            BackgroundTransparency = 1,
            Position = UDim2Offset(0, 0),
            Size = UDim2Offset(BoxWidth - 2, 4000),
            ZIndex = Window:NextZ(),
            Parent = Clip
        }).Instance

        local SelectionBox = {
            Items = Items_,
            Selected = nil,
            Flag = Flag,
            Tab = self.Tab,
            Scroll = 0,
            MaxScroll = 0,
            ViewH = BoxHeight - 2,
            Clip = Clip,
            Inner = Inner,
            RowObjects = { }
        }

        local function Apply()
            Library:SetFlag(Flag, SelectionBox.Selected)
            Library:SafeCall(Callback, SelectionBox.Selected)
        end

        function SelectionBox:Refresh(NewItems)
            if NewItems then
                self.Items = NewItems
            end
            for Index, Object in self.RowObjects do
                Object:Destroy()
            end
            self.RowObjects = { }

            if self.Selected then
                local Found = false
                for Index = 1, #self.Items do
                    if self.Items[Index] == self.Selected then Found = true break end
                end
                if not Found then self.Selected = nil end
            end

            for Index = 1, #self.Items do
                local ItemName = self.Items[Index]
                local RowY = (Index - 1) * RowH

                local Row = Instances:Create("TextButton", {
                    Name = "\0",
                    BackgroundColor3 = Theme.Hover,
                    BackgroundTransparency = (ItemName == self.Selected) and 0 or 1,
                    BorderSizePixel = 0,
                    AutoButtonColor = false,
                    Active = true,
                    FontFace = Library.Font,
                    TextSize = Sizes.Tab,
                    TextColor3 = (ItemName == self.Selected) and Theme.Accent or Theme.Text,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    TextYAlignment = Enum.TextYAlignment.Center,
                    Text = "   " .. ItemName,
                    Position = UDim2Offset(0, RowY),
                    Size = UDim2Offset(BoxWidth - 2, RowH),
                    ZIndex = Window:NextZ(),
                    Parent = Inner
                })
                TableInsert(self.RowObjects, Row.Instance)

                Row:Connect("MouseEnter", function()
                    if ItemName ~= self.Selected then Row.Instance.BackgroundTransparency = 0.55 end
                end)
                Row:Connect("MouseLeave", function()
                    if ItemName ~= self.Selected then Row.Instance.BackgroundTransparency = 1 end
                end)
                Row:Connect("MouseButton1Click", function()
                    self.Selected = ItemName
                    for RowIndex, OtherName in self.Items do
                        local Obj = self.RowObjects[RowIndex]
                        if Obj then
                            Obj.BackgroundTransparency = (OtherName == ItemName) and 0 or 1
                            Obj.TextColor3 = (OtherName == ItemName) and Theme.Accent or Theme.Text
                        end
                    end
                    Window.FadeStatic = nil
                    Apply()
                end)
            end

            self.MaxScroll = MathMax(0, #self.Items * RowH - self.ViewH)
            self.Scroll = MathClamp(self.Scroll, 0, self.MaxScroll)
            Inner.Position = UDim2Offset(0, -self.Scroll)
            Window.FadeStatic = nil
        end

        function SelectionBox:Get()
            return self.Selected
        end

        function SelectionBox:Set(Value)
            self.Selected = Value
            for RowIndex, ItemName in self.Items do
                local Obj = self.RowObjects[RowIndex]
                if Obj then
                    Obj.BackgroundTransparency = (ItemName == Value) and 0 or 1
                    Obj.TextColor3 = (ItemName == Value) and Theme.Accent or Theme.Text
                end
            end
            Apply()
        end

        TableInsert(Window.SelectionBoxes, SelectionBox)
        if Flag then
            Library.SetFlags[Flag] = function(Value) SelectionBox:Set(Value) end
        end

        SelectionBox:Refresh()
        self:Fit()
        return SelectionBox
    end

    Library.KeybindList = function(self)
        local Window = self
        local Gui = self.Gui

        local MaxWidth = 240
        local PadX = 6
        local RowHeight = 15
        local HeadHeight = 24
        local BottomPad = 5

        local List = {
            Rows = { },
            Width = 90
        }

        local TitleWidth = self:Measure("keybinds") + 4

        local Items = { } do
            Items["Box"] = Instances:Create("Frame", {
                Name = "\0",
                BackgroundColor3 = Theme.Fill,
                BorderSizePixel = 0,
                Active = true,
                Size = UDim2Offset(MaxWidth, HeadHeight + BottomPad),
                Position = UDim2Offset(12, 120),
                ZIndex = 7000,
                Parent = Gui
            }).Instance

            Items["OuterTop"] = self:Rect(Items["Box"], 0, 0, MaxWidth, 1, Theme.Outer, 7003)
            Items["OuterBottom"] = self:Rect(Items["Box"], 0, 1, MaxWidth, 1, Theme.Outer, 7003)
            Items["OuterLeft"] = self:Rect(Items["Box"], 0, 0, 1, 1, Theme.Outer, 7003)
            Items["OuterRight"] = self:Rect(Items["Box"], 1, 0, 1, 1, Theme.Outer, 7003)
            Items["InnerTop"] = self:Rect(Items["Box"], 1, 1, MaxWidth - 2, 1, Theme.Inner, 7002)
            Items["InnerBottom"] = self:Rect(Items["Box"], 1, 2, MaxWidth - 2, 1, Theme.Inner, 7002)
            Items["InnerLeft"] = self:Rect(Items["Box"], 1, 1, 1, 1, Theme.Inner, 7002)
            Items["InnerRight"] = self:Rect(Items["Box"], 2, 1, 1, 1, Theme.Inner, 7002)

            Items["Accent"] = self:Rect(Items["Box"], 2, 2, MaxWidth - 4, 2, Theme.Accent, 7005)
            Items["Accent"].ClipsDescendants = true
            TableInsert(self.AccentSolids, Items["Accent"])
            self:AddShimmer(Items["Accent"], MaxWidth - 4, 2, nil, nil, 7006)

            self:Glyph(Items["Box"], Sizes.Tab, PadX + 1, 14, 120, 14, "keybinds", Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5), 7004)
            self:Glyph(Items["Box"], Sizes.Tab, PadX, 13, 120, 14, "keybinds", Theme.Text,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5), 7004)

            Items["Divider"] = self:Rect(Items["Box"], 2, 21, MaxWidth - 4, 1, Theme.Inner, 7004)
        end

        local Box = Items["Box"]
        List.Box = Box
        self.KeybindBox = Box

        function List:Refresh()
            local Widest = TitleWidth
            local Visible = 0

            for Index, Row in List.Rows do
                if Row.Visible then
                    Visible = Visible + 1
                    local RowWidth = Row.NameW + Row.KeyW + 14
                    if RowWidth > Widest then
                        Widest = RowWidth
                    end
                end
            end

            local Width = MathClamp(Widest + PadX * 2, 90, MaxWidth)
            local Height = HeadHeight + Visible * RowHeight + BottomPad
            List.Width = Width

            Box.Size = UDim2Offset(Width, Height)
            Items["OuterTop"].Size = UDim2Offset(Width, 1)
            Items["OuterBottom"].Position = UDim2Offset(0, Height - 1)
            Items["OuterBottom"].Size = UDim2Offset(Width, 1)
            Items["OuterLeft"].Size = UDim2Offset(1, Height)
            Items["OuterRight"].Position = UDim2Offset(Width - 1, 0)
            Items["OuterRight"].Size = UDim2Offset(1, Height)
            Items["InnerTop"].Size = UDim2Offset(Width - 2, 1)
            Items["InnerBottom"].Position = UDim2Offset(1, Height - 2)
            Items["InnerBottom"].Size = UDim2Offset(Width - 2, 1)
            Items["InnerLeft"].Size = UDim2Offset(1, Height - 2)
            Items["InnerRight"].Position = UDim2Offset(Width - 2, 1)
            Items["InnerRight"].Size = UDim2Offset(1, Height - 2)
            Items["Accent"].Size = UDim2Offset(Width - 4, 2)
            Items["Divider"].Size = UDim2Offset(Width - 4, 1)

            local Y = HeadHeight
            for Index, Row in List.Rows do
                if Row.Visible then
                    local CenterY = Y + MathFloor(RowHeight / 2)
                    Row.NameMain.Visible = true
                    Row.NameShadow.Visible = true
                    Row.KeyMain.Visible = true
                    Row.KeyShadow.Visible = true
                    Row.NameMain.Position = UDim2Offset(PadX, CenterY)
                    Row.NameShadow.Position = UDim2Offset(PadX + 1, CenterY + 1)
                    Row.KeyMain.Position = UDim2Offset(Width - PadX, CenterY)
                    Row.KeyShadow.Position = UDim2Offset(Width - PadX + 1, CenterY + 1)
                    Y = Y + RowHeight
                else
                    Row.NameMain.Visible = false
                    Row.NameShadow.Visible = false
                    Row.KeyMain.Visible = false
                    Row.KeyShadow.Visible = false
                end
            end
        end

        function List:Add(Keybind)
            local NameShadow = Window:Glyph(Box, Sizes.Tab, PadX + 1, 0, 130, 14, Keybind.Name, Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5), 7004)
            local NameMain = Window:Glyph(Box, Sizes.Tab, PadX, 0, 130, 14, Keybind.Name, Theme.Text,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Center, Vector2New(0, 0.5), 7004)
            local KeyShadow = Window:Glyph(Box, Sizes.Tab, 0, 0, 70, 14, "[-]", Theme.Outer,
                Enum.TextXAlignment.Right, Enum.TextYAlignment.Center, Vector2New(1, 0.5), 7004)
            local KeyMain = Window:Glyph(Box, Sizes.Tab, 0, 0, 70, 14, "[-]", Theme.Text,
                Enum.TextXAlignment.Right, Enum.TextYAlignment.Center, Vector2New(1, 0.5), 7004)

            local Row = {
                Keybind = Keybind,
                NameShadow = NameShadow,
                NameMain = NameMain,
                KeyShadow = KeyShadow,
                KeyMain = KeyMain,
                NameW = Window:Measure(Keybind.Name),
                KeyW = 0,
                Visible = false
            }

            function Row:Update()
                local KeyText = "[" .. Library:KeyName(Keybind.Key) .. "]"
                Row.KeyMain.Text = KeyText
                Row.KeyShadow.Text = KeyText
                Row.KeyW = Window:Measure(KeyText)
                Row.Visible = Keybind.Key ~= nil
            end

            function Row:SetActive(Bool)
                local Color = Bool and Theme.Accent or Theme.Text
                Row.NameMain.TextColor3 = Color
                Row.KeyMain.TextColor3 = Color
            end

            TableInsert(List.Rows, Row)
            Row:Update()
            Row:SetActive(Keybind.Active)
            List:Refresh()
            return Row
        end

        function List:SetVisibility(Bool)
            Box.Visible = Bool
        end

        Library:Connect(Box.InputBegan, function(Input)
            if Input.UserInputType == Enum.UserInputType.MouseButton1 then
                Window.KbDragging = true
                Window.KbOff = Vector2New(Mouse.X - Box.AbsolutePosition.X, Mouse.Y - Box.AbsolutePosition.Y)
            end
        end)

        self.KeyList = List

        for Index, Keybind in self.Keybinds do
            Keybind.ListItem = List:Add(Keybind)
        end

        List:Refresh()
        return List
    end

    Library.Animate = function(self, DeltaTime)
        local Step = self.Animations and (DeltaTime / 0.15) or 1

        if self.Dragging then
            local Viewport = Workspace.CurrentCamera and Workspace.CurrentCamera.ViewportSize or Vector2New(1280, 800)
            local Size = self.Frame.AbsoluteSize
            self.Frame.Position = UDim2Offset(
                MathClamp(Mouse.X - self.DragOff.X, 0, Viewport.X - Size.X),
                MathClamp(Mouse.Y - self.DragOff.Y, 0, Viewport.Y - Size.Y))
        end

        if self.WmDragging and self.Watermark then
            local Viewport = Workspace.CurrentCamera and Workspace.CurrentCamera.ViewportSize or Vector2New(1280, 800)
            local Size = self.Watermark.AbsoluteSize
            self.Watermark.Position = UDim2Offset(
                MathClamp(Mouse.X - self.WmOff.X, 0, Viewport.X - Size.X),
                MathClamp(Mouse.Y - self.WmOff.Y, 0, Viewport.Y - Size.Y))
        end

        if self.KbDragging and self.KeybindBox then
            local Viewport = Workspace.CurrentCamera and Workspace.CurrentCamera.ViewportSize or Vector2New(1280, 800)
            local Size = self.KeybindBox.AbsoluteSize
            self.KeybindBox.Position = UDim2Offset(
                MathClamp(Mouse.X - self.KbOff.X, 0, Viewport.X - Size.X),
                MathClamp(Mouse.Y - self.KbOff.Y, 0, Viewport.Y - Size.Y))
        end

        if self.WmReady and self.Watermark.Visible then
            self.WmAcc = (self.WmAcc or 0) + DeltaTime
            self.WmCnt = (self.WmCnt or 0) + 1
            self.WmTimer = (self.WmTimer or 0) + DeltaTime
            if self.WmTimer >= 0.5 then
                local Fps = MathFloor(self.WmCnt / self.WmAcc + 0.5)
                self.WmTimer, self.WmAcc, self.WmCnt = 0, 0, 0
                if Fps ~= self.WmShown then
                    self.WmShown = Fps
                    self.WmSetFps(Fps)
                end
            end
        end

        local AlphaStep = DeltaTime / 0.2
        local AlphaTarget = self.Open and 1 or 0
        if self.Alpha < AlphaTarget then
            self.Alpha = MathMin(self.Alpha + AlphaStep, AlphaTarget)
        elseif self.Alpha > AlphaTarget then
            self.Alpha = MathMax(self.Alpha - AlphaStep, AlphaTarget)
        end
        local Alpha = self.Alpha
        self.Frame.Visible = Alpha > 0

        local Height = self.Height
        for Index, Tab in self.Tabs do
            local Target = (Index == self.Selected) and 1 or 0
            if Tab.Fade < Target then
                Tab.Fade = MathMin(Tab.Fade + Step, Target)
            elseif Tab.Fade > Target then
                Tab.Fade = MathMax(Tab.Fade - Step, Target)
            end
            local Fade = Tab.Fade
            Tab.FillFrame.BackgroundTransparency = 1 - Fade * Alpha
            local Brightness = MathFloor(85 + (204 - 85) * Fade)
            Tab.Label.TextColor3 = FromRGB(Brightness, Brightness, Brightness)
            Tab.Indicator.Visible = Fade > 0
            Tab.Indicator.BackgroundTransparency = 1 - Fade * Alpha
            Tab.Content.Visible = (Index == self.Selected)

            local MaxCol = MathMax(Tab.ColY[0], Tab.ColY[1])
            local ContentBot = MaxCol + 58
            Tab.MaxScroll = MathMax(0, ContentBot - (Height - 4))
            Tab.ScrollTarget = MathClamp(Tab.ScrollTarget, 0, Tab.MaxScroll)
            local Ease = self.Animations and (1 - math.exp(-DeltaTime * 16)) or 1
            Tab.Scroll = Tab.Scroll + (Tab.ScrollTarget - Tab.Scroll) * Ease
            Tab.Scroll = MathClamp(Tab.Scroll, 0, Tab.MaxScroll)
            Tab.Scroller.Position = UDim2Offset(0, -MathFloor(Tab.Scroll + 0.5))

            if Tab.MaxScroll > 0 then
                local Remain = Tab.MaxScroll - Tab.Scroll
                local Fog = MathClamp(Remain / 24, 0, 1)
                local Ga = 120 * Fog
                Tab.Shade.Visible = Ga > 0.5
                Tab.Shade.BackgroundTransparency = 1 - (Ga / 255) * Alpha

                local VisibleH = Height - 59
                local Total = MaxCol + 3
                local TrackH = Height - 63
                local ThumbH = MathMax(12, TrackH * (VisibleH / Total))
                local Frac = Tab.Scroll / Tab.MaxScroll
                local ThumbY = 1 + (TrackH - ThumbH) * Frac
                Tab.Thumb.Visible = true
                Tab.Thumb.Position = UDim2Offset(self.Width - 14, MathFloor(ThumbY + 0.5))
                Tab.Thumb.Size = UDim2Offset(2, MathFloor(ThumbH + 0.5))
            else
                Tab.Shade.Visible = false
                Tab.Thumb.Visible = false
            end
        end

        local AbsPos = self.Frame.AbsolutePosition
        for Index, Popup in self.ScrollPopups do
            Popup.Popup.Position = UDim2Offset(
                MathFloor(AbsPos.X + Popup.X + 0.5),
                MathFloor(AbsPos.Y + Popup.Y - Popup.Tab.Scroll + 0.5))
        end

        for Index, Toggle in self.Toggles do
            local Target = Toggle.Value and 1 or 0
            if Toggle.Fade < Target then
                Toggle.Fade = MathMin(Toggle.Fade + Step, Target)
            elseif Toggle.Fade > Target then
                Toggle.Fade = MathMax(Toggle.Fade - Step, Target)
            end
            Toggle.AccentLayer.BackgroundTransparency = 1 - Toggle.Fade * Alpha
            local Brightness = MathFloor(85 + (204 - 85) * Toggle.Fade)
            Toggle.LabelText.TextColor3 = FromRGB(Brightness, Brightness, Brightness)
        end

        local HoverStep = self.Animations and (DeltaTime / 0.12) or 1
        for Index, Row in self.KeyRows do
            local Target = Row.Hovered and 1 or 0
            if Row.HP < Target then
                Row.HP = MathMin(Row.HP + HoverStep, Target)
            elseif Row.HP > Target then
                Row.HP = MathMax(Row.HP - HoverStep, Target)
            end
            local OffsetX = Row.HP * 4
            local RowY = 1 + Row.Index * Row.RowH
            Row.Main.Position = UDim2Offset(5 + OffsetX, RowY)
            Row.Shadow.Position = UDim2Offset(6 + OffsetX, RowY)
            Row.Highlight.BackgroundTransparency = Row.Hovered and 0 or 1
        end

        for Index, Row in self.MultiRows do
            local Target = Row.Mask[Row.Index] and 1 or 0
            if Row.Fade < Target then
                Row.Fade = MathMin(Row.Fade + Step, Target)
            elseif Row.Fade > Target then
                Row.Fade = MathMax(Row.Fade - Step, Target)
            end
            local Brightness = MathFloor(85 + (204 - 85) * Row.Fade)
            Row.Main.TextColor3 = FromRGB(Brightness, Brightness, Brightness)
        end

        for Index, Slider in self.Sliders do
            if Slider.Dragging then
                local Ratio = MathClamp((Mouse.X - Slider.Bar.AbsolutePosition.X) / Slider.Bar.AbsoluteSize.X, 0, 1)
                Slider.Value = Slider.Min + (Slider.Max - Slider.Min) * Ratio
                Slider.Update()
            end
        end

        for Index, Drag in self.Drags do
            if Drag.Dragging then
                local Position, Size = Drag.Area.AbsolutePosition, Drag.Area.AbsoluteSize
                local TX = (Size.X > 0) and MathClamp((Mouse.X - Position.X) / Size.X, 0, 1) or 0
                local TY = (Size.Y > 0) and MathClamp((Mouse.Y - Position.Y) / Size.Y, 0, 1) or 0
                Drag.Move(TX, TY)
            end
        end

        if not Library.Shimmer.Enabled then
            for Index, Segment in self.Shimmers do
                Segment.Frame.BackgroundTransparency = 1
            end
        else
            local Phase = (os.clock() * (Library.Shimmer.Speed * 0.01)) % 1
            local Falloff = 3.0 - Library.Shimmer.Width * 0.02
            for Index, Segment in self.Shimmers do
                local Distance = Segment.Scalar - Phase
                if Distance > 0.5 then Distance = Distance - 1 end
                if Distance < -0.5 then Distance = Distance + 1 end
                if Distance < 0 then Distance = -Distance end
                local Boost = 1 - Distance * Falloff
                if Boost <= 0 then
                    Segment.Frame.BackgroundTransparency = 1
                else
                    Boost = Boost * Boost
                    local Multiplier = Segment.Fade and Segment.Fade.Fade or 1
                    if Segment.InMenu == nil then
                        Segment.InMenu = Segment.Frame:IsDescendantOf(self.Frame)
                    end
                    if Segment.InMenu then
                        Multiplier = Multiplier * Alpha
                    end
                    local Sa = MathMin(Boost * Library.Shimmer.Glow * Multiplier, 255)
                    Segment.Frame.BackgroundTransparency = 1 - (Sa / 255)
                end
            end
        end

        local Fading = Alpha < 1
        if Fading or self.FadeDirty then
            if not self.FadeStatic then
                local Skip = { }
                for Index, Tab in self.Tabs do
                    Skip[Tab.FillFrame] = true
                    Skip[Tab.Indicator] = true
                    Skip[Tab.Shade] = true
                end
                for Index, Toggle in self.Toggles do
                    Skip[Toggle.AccentLayer] = true
                end
                for Index, Segment in self.Shimmers do
                    if Segment.Frame:IsDescendantOf(self.Frame) then
                        Skip[Segment.Frame] = true
                    end
                end

                self.FadeStatic = { }
                for Index, Object in self.Frame:GetDescendants() do
                    if not Skip[Object] then
                        if Object:IsA("GuiObject") then
                            TableInsert(self.FadeStatic, { Object, "BackgroundTransparency", Object.BackgroundTransparency })
                            if Object:IsA("TextLabel") or Object:IsA("TextButton") or Object:IsA("TextBox") then
                                TableInsert(self.FadeStatic, { Object, "TextTransparency", Object.TextTransparency })
                                TableInsert(self.FadeStatic, { Object, "TextStrokeTransparency", Object.TextStrokeTransparency })
                            elseif Object:IsA("ImageLabel") or Object:IsA("ImageButton") then
                                TableInsert(self.FadeStatic, { Object, "ImageTransparency", Object.ImageTransparency })
                            end
                        end
                    end
                end
            end

            for Index, Record in self.FadeStatic do
                Record[1][Record[2]] = 1 - (1 - Record[3]) * Alpha
            end
        end
        self.FadeDirty = Fading
    end

    Library.CreateWatermark = function(self, WmName, WmBuild)
        local Gui = self.Gui
        local Pad, TopGap, InnerHeight, InnerPadX, Gap = 6, 8, 18, 10, 7
        local BoxHeight = TopGap + InnerHeight + Pad

        local Watermark = Instances:Create("Frame", {
            Name = "\0",
            BackgroundColor3 = Theme.Fill,
            BorderSizePixel = 0,
            Active = true,
            Size = UDim2Offset(200, BoxHeight),
            Position = UDim2Offset(12, 12),
            ZIndex = 8000,
            Parent = Gui
        }).Instance
        self.Watermark = Watermark

        local OuterTop = self:Rect(Watermark, 0, 0, 200, 1, Theme.Outer, 8003)
        local OuterBottom = self:Rect(Watermark, 0, BoxHeight - 1, 200, 1, Theme.Outer, 8003)
        local OuterRight = self:Rect(Watermark, 199, 0, 1, BoxHeight, Theme.Outer, 8003)
        self:Rect(Watermark, 0, 0, 1, BoxHeight, Theme.Outer, 8003)
        local InnerTop = self:Rect(Watermark, 1, 1, 198, 1, Theme.Inner, 8002)
        local InnerBottom = self:Rect(Watermark, 1, BoxHeight - 2, 198, 1, Theme.Inner, 8002)
        local InnerRight = self:Rect(Watermark, 198, 1, 1, BoxHeight - 2, Theme.Inner, 8002)
        self:Rect(Watermark, 1, 1, 1, BoxHeight - 2, Theme.Inner, 8002)

        local InnerFill = self:Rect(Watermark, Pad, TopGap, 188, InnerHeight, Theme.Section, 8004)
        local InnerBoxBT = self:Rect(Watermark, Pad, TopGap, 188, 1, Theme.Outer, 8006)
        local InnerBoxBB = self:Rect(Watermark, Pad, TopGap + InnerHeight - 1, 188, 1, Theme.Outer, 8006)
        local InnerBoxBR = self:Rect(Watermark, Pad + 187, TopGap, 1, InnerHeight, Theme.Outer, 8006)
        self:Rect(Watermark, Pad, TopGap, 1, InnerHeight, Theme.Outer, 8006)
        local InnerBoxIT = self:Rect(Watermark, Pad + 1, TopGap + 1, 186, 1, Theme.Inner, 8005)
        local InnerBoxIB = self:Rect(Watermark, Pad + 1, TopGap + InnerHeight - 2, 186, 1, Theme.Inner, 8005)
        local InnerBoxIR = self:Rect(Watermark, Pad + 186, TopGap + 1, 1, InnerHeight - 2, Theme.Inner, 8005)
        self:Rect(Watermark, Pad + 1, TopGap + 1, 1, InnerHeight - 2, Theme.Inner, 8005)

        local AccentBar = self:Rect(Watermark, Pad, TopGap - 3, 188, 2, Theme.Accent, 8005)
        AccentBar.ClipsDescendants = true
        TableInsert(self.AccentSolids, AccentBar)

        local TextY = TopGap + 2 + MathFloor((InnerHeight - 4 - Sizes.Tab) * 0.5)

        local function Piece(Text, Color)
            local Shadow = self:Glyph(Watermark, Sizes.Tab, 0, TextY + 1, 90, InnerHeight, Text, Theme.Outer,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Top, Vector2New(0, 0), 8007)
            local Main = self:Glyph(Watermark, Sizes.Tab, 0, TextY, 90, InnerHeight, Text, Color,
                Enum.TextXAlignment.Left, Enum.TextYAlignment.Top, Vector2New(0, 0), 8007)
            return { Shadow = Shadow, Main = Main }
        end

        local PieceName = Piece(WmName, Theme.Text)
        local PieceSep1 = Piece("/", Theme.Accent)
        local PieceFps = Piece("fps: 0", Theme.Text)
        local PieceSep2 = Piece("/", Theme.Accent)
        local PieceBuild = Piece("build: " .. WmBuild, Theme.Text)
        TableInsert(self.AccentTexts, PieceSep1.Main)
        TableInsert(self.AccentTexts, PieceSep2.Main)

        local WidthName, WidthSep, WidthFps, WidthBuild = 0, 0, 0, 0

        local function SetX(PieceData, X)
            PieceData.Main.Position = UDim2Offset(X, TextY)
            PieceData.Shadow.Position = UDim2Offset(X + 1, TextY + 1)
        end

        local function Layout()
            local InnerWidth = (WidthName + WidthSep + WidthFps + WidthSep + WidthBuild + Gap * 4) + InnerPadX * 2
            local TotalWidth = InnerWidth + Pad * 2
            Watermark.Size = UDim2Offset(TotalWidth, BoxHeight)
            OuterTop.Size = UDim2Offset(TotalWidth, 1)
            OuterBottom.Size = UDim2Offset(TotalWidth, 1)
            OuterRight.Position = UDim2Offset(TotalWidth - 1, 0)
            InnerTop.Size = UDim2Offset(TotalWidth - 2, 1)
            InnerBottom.Size = UDim2Offset(TotalWidth - 2, 1)
            InnerRight.Position = UDim2Offset(TotalWidth - 2, 1)
            InnerFill.Size = UDim2Offset(InnerWidth, InnerHeight)
            InnerBoxBT.Size = UDim2Offset(InnerWidth, 1)
            InnerBoxBB.Size = UDim2Offset(InnerWidth, 1)
            InnerBoxBR.Position = UDim2Offset(Pad + InnerWidth - 1, TopGap)
            InnerBoxIT.Size = UDim2Offset(InnerWidth - 2, 1)
            InnerBoxIB.Size = UDim2Offset(InnerWidth - 2, 1)
            InnerBoxIR.Position = UDim2Offset(Pad + InnerWidth - 2, TopGap + 1)
            AccentBar.Size = UDim2Offset(InnerWidth, 2)
            local X = Pad + InnerPadX
            SetX(PieceName, X); X = X + WidthName + Gap
            SetX(PieceSep1, X); X = X + WidthSep + Gap
            SetX(PieceFps, X); X = X + WidthFps + Gap
            SetX(PieceSep2, X); X = X + WidthSep + Gap
            SetX(PieceBuild, X)
        end

        self.WmSetFps = function(Fps)
            local Text = "fps: " .. Fps
            PieceFps.Main.Text = Text
            PieceFps.Shadow.Text = Text
            task.spawn(function()
                WidthFps = self:Measure(Text)
                Layout()
            end)
        end

        self.WmSetName = function(Name)
            PieceName.Main.Text = Name
            PieceName.Shadow.Text = Name
            task.spawn(function()
                WidthName = self:Measure(Name)
                Layout()
            end)
        end

        self.WmSetBuild = function(Build)
            local Text = "build: " .. Build
            PieceBuild.Main.Text = Text
            PieceBuild.Shadow.Text = Text
            task.spawn(function()
                WidthBuild = self:Measure(Text)
                Layout()
            end)
        end

        task.spawn(function()
            WidthName = self:Measure(WmName)
            WidthSep = self:Measure("/")
            WidthBuild = self:Measure("build: " .. WmBuild)
            WidthFps = self:Measure("fps: 0")
            local InnerWidth = (WidthName + WidthSep + WidthFps + WidthSep + WidthBuild + Gap * 4) + InnerPadX * 2
            self:AddShimmer(AccentBar, InnerWidth, 2, nil, nil, 8006)
            Layout()
            local Viewport = Workspace.CurrentCamera and Workspace.CurrentCamera.ViewportSize or Vector2New(1280, 800)
            Watermark.Position = UDim2Offset(MathFloor(Viewport.X - (InnerWidth + Pad * 2) - 12), 12)
            self.WmReady = true
        end)

        Library:Connect(Watermark.InputBegan, function(Input)
            if Input.UserInputType == Enum.UserInputType.MouseButton1 then
                self.WmDragging = true
                self.WmOff = Vector2New(Mouse.X - Watermark.AbsolutePosition.X, Mouse.Y - Watermark.AbsolutePosition.Y)
            end
        end)
    end

    Library.Window = function(self, Data)
        Data = Data or { }

        local Parent = gethui()
        local Old = Parent:FindFirstChild("coolui")
        if Old then
            Old:Destroy()
        end

        local Gui = Instances:Create("ScreenGui", {
            Name = "coolui",
            ZIndexBehavior = Enum.ZIndexBehavior.Global,
            ResetOnSpawn = false,
            Parent = Parent
        }).Instance
        Library.ScreenGui = Gui

        local Window = setmetatable({
            Gui = Gui,
            Z = 0,
            Tabs = { },
            Selected = 1,
            Width = Data.Width or Data.width or 373,
            Height = Data.Height or Data.height or 407,
            Animations = true,
            Open = true,
            Alpha = 1,

            Shimmers = { },
            Toggles = { },
            KeyRows = { },
            MultiRows = { },
            Sliders = { },
            Drags = { },
            ScrollPopups = { },
            SelectionBoxes = { },
            OpenPopup = nil,

            AccentSolids = { },
            AccentTexts = { },
            AccentGrads = { },
            Dropdowns = { },
            Keybinds = { },
            KeyList = nil,

            TitlePre = Data.Title or Data.title or "juanita",
            TitleAccent = Data.Accent or Data.accent or "haxx",
            TitlePost = Data.Info or Data.info or " | uid 1337"
        }, Library)

        local Width, Height = Window.Width, Window.Height

        local Items = { } do
            Items["Frame"] = Instances:Create("Frame", {
                Name = "\0",
                BackgroundTransparency = 1,
                Size = UDim2Offset(Width, Height),
                ZIndex = Window:NextZ(),
                Parent = Gui
            }).Instance
            Window.Frame = Items["Frame"]

            local Viewport = Workspace.CurrentCamera and Workspace.CurrentCamera.ViewportSize or Vector2New(1280, 800)
            Items["Frame"].Position = UDim2Offset(MathFloor(Viewport.X / 2 - Width / 2), MathFloor(Viewport.Y / 2 - Height / 2))

            Items["Blocker"] = Instances:Create("TextButton", {
                Name = "\0",
                BackgroundTransparency = 1,
                AutoButtonColor = false,
                Active = true,
                Text = "",
                Size = UDim2Scale(1, 1),
                Visible = false,
                ZIndex = 9000,
                Parent = Gui
            })
            Window.Blocker = Items["Blocker"].Instance
            Items["Blocker"]:Connect("MouseButton1Click", function()
                if Window.OpenPopup then
                    Window.OpenPopup.Popup.Visible = false
                    Window.OpenPopup = nil
                end
                Window.Blocker.Visible = false
            end)

            Window:Rect(Items["Frame"], 0, 0, Width, Height, Theme.Fill)
            Window:Outline(Items["Frame"], 1, 1, Width - 2, Height - 2, Theme.Inner)
            Window:Outline(Items["Frame"], 0, 0, Width, Height, Theme.Outer)

            Items["TitleHolder"] = Instances:Create("Frame", {
                Name = "\0",
                BackgroundTransparency = 1,
                Size = UDim2Offset(Width, 26),
                ZIndex = Window:NextZ(),
                Parent = Items["Frame"]
            }).Instance
            Window.TitleHolder = Items["TitleHolder"]
            Window:DrawTitle()

            Items["TitleDrag"] = Instances:Create("Frame", {
                Name = "\0",
                BackgroundTransparency = 1,
                Active = true,
                Size = UDim2Offset(Width, 26),
                ZIndex = Window:NextZ(),
                Parent = Items["Frame"]
            })
            Items["TitleDrag"]:Connect("InputBegan", function(Input)
                if Input.UserInputType == Enum.UserInputType.MouseButton1 then
                    Window.Dragging = true
                    Window.DragOff = Vector2New(Mouse.X - Items["Frame"].AbsolutePosition.X, Mouse.Y - Items["Frame"].AbsolutePosition.Y)
                end
            end)

            local Bar = Window:Rect(Items["Frame"], 4, 26, Width - 8, 29, FromRGB(255, 255, 255))
            Instances:Create("UIGradient", {
                Name = "\0",
                Rotation = 90,
                Color = RGBSequence(Theme["Bar Top"], Theme["Bar Bottom"]),
                Parent = Bar
            })
            Window:Rect(Items["Frame"], 5, 27, Width - 10, 1, Theme.Inner)
            Window:Rect(Items["Frame"], 5, 53, Width - 10, 1, Theme.Inner)
            Window:Rect(Items["Frame"], 5, 27, 1, 27, Theme.Inner)
            Window:Rect(Items["Frame"], Width - 6, 27, 1, 27, Theme.Inner)
            Window:Outline(Items["Frame"], 4, 26, Width - 8, 29, Theme.Outer)

            Window:Rect(Items["Frame"], 4, 55, Width - 8, Height - 59, Theme.Fill)
            Window:Rect(Items["Frame"], 4, 55, 1, Height - 59, Theme.Outer)
            Window:Rect(Items["Frame"], Width - 5, 55, 1, Height - 59, Theme.Outer)
            Window:Rect(Items["Frame"], 4, Height - 5, Width - 8, 1, Theme.Outer)
            Window:Rect(Items["Frame"], 5, 55, Width - 10, 1, Theme.Inner)
            Window:Rect(Items["Frame"], 5, 55, 1, Height - 60, Theme.Inner)
            Window:Rect(Items["Frame"], Width - 6, 55, 1, Height - 60, Theme.Inner)
            Window:Rect(Items["Frame"], 5, Height - 6, Width - 10, 1, Theme.Inner)
        end

        Window.Elements = Items

        Window:CreateWatermark(Data.WatermarkName or "eskolz", Data.WatermarkBuild or "beta")

        Library:Connect(UserInputService.InputBegan, function(Input, GameProcessed)
            if GameProcessed then
                return
            end

            local Menu = Window.MenuKey
            if not Menu or Menu.Listening or Menu.Key == nil then
                return
            end

            local Key = Menu.Key
            local Hit = (Input.UserInputType == Enum.UserInputType.Keyboard and Input.KeyCode == Key)
                or (Key == "m1" and Input.UserInputType == Enum.UserInputType.MouseButton1)
                or (Key == "m2" and Input.UserInputType == Enum.UserInputType.MouseButton2)
                or (Key == "m3" and Input.UserInputType == Enum.UserInputType.MouseButton3)

            if Hit then
                Window.Open = not Window.Open
                if not Window.Open then
                    if Window.OpenPopup then
                        Window.OpenPopup.Popup.Visible = false
                        Window.OpenPopup = nil
                    end
                    Window.Blocker.Visible = false
                end
            end
        end)

        Library:Connect(UserInputService.InputEnded, function(Input)
            if Input.UserInputType == Enum.UserInputType.MouseButton1 then
                for Index, Slider in Window.Sliders do
                    Slider.Dragging = false
                end
                for Index, Drag in Window.Drags do
                    Drag.Dragging = false
                end
                Window.WmDragging = false
                Window.KbDragging = false
                Window.Dragging = false
            end
        end)

        Library:Connect(UserInputService.InputChanged, function(Input)
            if Input.UserInputType ~= Enum.UserInputType.MouseWheel then
                return
            end
            if not Window.Frame.Visible then
                return
            end

            for Index, SelectionBox in Window.SelectionBoxes do
                if SelectionBox.MaxScroll > 0 and SelectionBox.Tab == Window.Tabs[Window.Selected] then
                    local SbAbs = SelectionBox.Clip.AbsolutePosition
                    local SbSize = SelectionBox.Clip.AbsoluteSize
                    if Mouse.X >= SbAbs.X and Mouse.X <= SbAbs.X + SbSize.X
                        and Mouse.Y >= SbAbs.Y and Mouse.Y <= SbAbs.Y + SbSize.Y then
                        SelectionBox.Scroll = MathClamp(SelectionBox.Scroll - Input.Position.Z * 16, 0, SelectionBox.MaxScroll)
                        SelectionBox.Inner.Position = UDim2Offset(0, -SelectionBox.Scroll)
                        return
                    end
                end
            end

            local Tab = Window.Tabs[Window.Selected]
            if not Tab or Tab.MaxScroll <= 0 then
                return
            end

            local Abs = Window.Frame.AbsolutePosition
            local X0, X1 = Abs.X + 5, Abs.X + Window.Width - 5
            local Y0, Y1 = Abs.Y + 55, Abs.Y + Window.Height - 4

            if Mouse.X >= X0 and Mouse.X <= X1 and Mouse.Y >= Y0 and Mouse.Y <= Y1 then
                Tab.ScrollTarget = MathClamp(Tab.ScrollTarget - Input.Position.Z * 42, 0, Tab.MaxScroll)
            end
        end)

        Library:Connect(RunService.RenderStepped, function(DeltaTime)
            if not Gui.Parent then
                return
            end
            Window:Animate(DeltaTime)
        end)

        return Window
    end
end

if getgenv then
    getgenv().Library = Library
end

return Library
