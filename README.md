# Nelsinhong
 ... 


<?xml version="1.0" encoding="utf-8" ?><ROGUE><Ability><Name>Event Loader</Name><Default>false</Default><SpellID>0</SpellID><Actions></Actions><Lua>if not cpoinit then
	cpoinit = true
	-- Create our frame and bind combat log events
	ToolkitFrame = CreateFrame(&amp;quot;Frame&amp;quot;, nil, UIParent)
	
	--Amount of Cooldown that remains
	function cdRemains(spellid)
		if select(2,GetSpellCooldown(spellid)) + (select(1,GetSpellCooldown(spellid)) - GetTime()) &amp;gt; 0
			then return select(2,GetSpellCooldown(spellid)) + (select(1,GetSpellCooldown(spellid)) - GetTime())
		else return 0
		end
	end



	

	Evasion = 26669
	CloakOfShadows = 31224
	ShadowStep = 36554
	Shiv = 5938
	Vanish = 26889
	Sap = 51724
	Blind = 2094
	Feint = 48659
	Gouge = 1776
	Dismantle = 51722


	notarspells = {
	-- Rogue
	[2094]  = {Vanish},			-- Blind
	[1833]  = {Feint},			-- Cheap Shot
	--[Gouge]  = {Feint},				-- test
	-- Druid
	[33786] = {CloakOfShadows},		-- Cyclone
	-- Mage
	[44572] = {CloakOfShadows},		-- Deep Freeze		
	[118]   = {CloakOfShadows},		-- Polymorph
	[122]   = {Feint},			-- Frost Nova
	[33395] = {Feint},			-- Pet Freeze
	--- Paladin
	[853]   = {Feint, CloakOfShadows},	-- Hammer of Justice
	[31884] = {Feint},			-- Avenging Wrath
	-- Priest
	[8122]  = {CloakOfShadows},		-- Psychic Scream							-- Shaman
	[51514] = {CloakOfShadows},		-- Hex
	-- Warlock
	[6789]  = {CloakOfShadows},		-- Death Coil
	[47860]  = {CloakOfShadows},	-- Death Coil Rank 6
	[5782]  = {CloakOfShadows},		-- Fear 
	[6215]  = {CloakOfShadows},		-- Fear Rank 3
	-- Warrior
	[1719] = {Feint}			-- Recklessness
	}



	-- Spell event table
	spellIds = {
	-- Death Knight
	[49576] = {ShadowStep},			-- Death Grip
	[49016] = {Shiv},		-- Unholy Frenzy
	-- Druid
	[5211]  = {Gouge},				-- Bash
	[33786] = {CloakOfShadows},			-- Cyclone
	[22570] = {Gouge, Evasion},	-- Maim
	[9005]  = {Gouge, Evasion},		-- Pounce
	[5229]  = {Shiv},				-- Enrage
	-- Hunter
	[781]   = {Gouge},		-- Disengage
	[19503] = {Gouge, CloakOfShadows},	-- Scatter Shot
	-- Mage
	[44572] = {Gouge, 	CloakOfShadows},		-- Deep Freeze					
	[31661] = {Gouge,  CloakOfShadows},	-- Dragon&amp;apos;s Breath
	[118]   = {CloakOfShadows},		-- Polymorph
	[122]   = {Gouge, Feint},	-- Frost Nova
	[33395] = {Gouge, Feint},		-- Pet Freeze
	[1953]  = {ShadowStep, Gouge},			-- Blink
	--- Paladin
	[853]   = {Gouge, Feint, CloakOfShadows,},	-- Hammer of Justice
	[31884] = {Feint, Dismantle},		-- Avenging Wrath
	-- Priest
	[64044] = {Gouge, Shiv},			-- Psychic Horror
	[8122]  = {CloakOfShadows, Gouge},	-- Psychic Scream
	[64058] = {Gouge, Shiv},			-- Psychic Horror
	-- Rogue
	[2094]  = {Vanish},	-- Blind
	[1833]  = {Dismantle, Feint, Gouge},			-- Cheap Shot
	[1856]  = {Sap, Dismantle,	 Blind},	 --Vanish		 
	[6770]  = {Sap},		-- Sap
	[51722] = {Dismantle},		-- Dismantle
										
	-- Shaman
	[51514] = {CloakOfShadows},		-- Hex

	-- Warlock
	[6789]  = {Gouge, CloakOfShadows},	-- Death Coil
	[5782]  = {CloakOfShadows},		-- Fear 
	-- Warrior
	[100]  = {ShadowStep},			-- Charge  
	[23694] = {Gouge, Dismantle},		-- Improved Hamstring 
	[1719] = {Dismantle, Feint},			-- Recklessness
	[18499] = {Shiv},					-- Beserker Rage
	}


	local SIN_PlayerGUID = UnitGUID(&amp;quot;player&amp;quot;)
	local SIN_InterruptFrame = CreateFrame(&amp;quot;FRAME&amp;quot;, nil, UIParent)
	SIN_InterruptFrame:RegisterEvent(&amp;quot;COMBAT_LOG_EVENT_UNFILTERED&amp;quot;)
	SIN_InterruptFrame:SetScript(&amp;quot;OnEvent&amp;quot;, 
		function(self, event, timestamp, type,  sourceGUID, sourceNAME, _, destGUID, destNAME, _, spellID)
			--print(&amp;quot;time stamp: &amp;quot;..timestamp)
			--print(&amp;quot;time: &amp;quot;..time())
			if type == &amp;quot;SPELL_CAST_SUCCESS&amp;quot;			
			and destGUID == SIN_PlayerGUID 
			and notarspells[spellID] then
				local sname = notarspells[spellID]
				for i=1, #sname do
					if cdRemains(sname[i]) == 0 then
						CastSpellByID(sname[i]) 
						RaidNotice_AddMessage(RaidWarningFrame, &amp;quot;\124cFFF00000\&amp;lt;Attempting to &amp;quot;..GetSpellInfo(sname[i])..&amp;quot; &amp;quot;..GetSpellInfo(spellID)..&amp;quot; from &amp;quot;..sourceNAME..&amp;quot;&amp;gt;&amp;quot;, ChatTypeInfo[&amp;quot;RAID_WARNING&amp;quot;])
						--print(&amp;quot;[&amp;quot;..timestamp-time()..&amp;quot; For Loop]Trying to counter &amp;quot;..GetSpellInfo(spellID)..&amp;quot; with &amp;quot;..GetSpellInfo(sname[i]))					
						return
					else
						return
					end
				end
			end		
		end)



	-- Create our event handler function
	function EventHandler(self, event, ...)
		local type,  sourceGUID, sourceNAME, _, destGUID, destNAME, _, sid = select(2, ...)
		if event == &amp;quot;COMBAT_LOG_EVENT_UNFILTERED&amp;quot; then
			if type == &amp;quot;SPELL_CAST_SUCCESS&amp;quot;  then
				
				
				--AntiCC
				local sname = spellIds[sid]
				if sname ~= nil then
					for i=1,  #sname do
						local cd = cdRemains(sname[i]) 
						
						
						if sname[i] == Sap      
						and cd == 0 
						and destGUID == UnitGUID(&amp;quot;player&amp;quot;) then
							SpellStopCasting()
							RaidNotice_AddMessage(RaidWarningFrame, &amp;quot;\124cFFF00000\&amp;lt;Attempting to &amp;quot;..GetSpellInfo(sname[i])..&amp;quot; &amp;quot;..GetSpellInfo(sid)..&amp;quot; from &amp;quot;..sourceNAME..&amp;quot;&amp;gt;&amp;quot;, ChatTypeInfo[&amp;quot;RAID_WARNING&amp;quot;])
							TargetUnit(sourceNAME)
							CastSpellByID(sname[i])
							return
						end
						
						if sname[i] == ShadowStep   
						and cd == 0 
						and destGUID == UnitGUID(&amp;quot;player&amp;quot;) then
							SpellStopCasting()
							RaidNotice_AddMessage(RaidWarningFrame, &amp;quot;\124cFFF00000\&amp;lt;Attempting to &amp;quot;..GetSpellInfo(sname[i])..&amp;quot; &amp;quot;..GetSpellInfo(sid)..&amp;quot; from &amp;quot;..sourceNAME..&amp;quot;&amp;gt;&amp;quot;, ChatTypeInfo[&amp;quot;RAID_WARNING&amp;quot;])
							TargetUnit(sourceNAME)
							CastSpellByID(sname[i])
							return
						end
						
						if sname[i] == Shiv    
						and cd == 0 
						and UnitCanAttack(&amp;quot;player&amp;quot;, sourceNAME)
						then
							RaidNotice_AddMessage(RaidWarningFrame, &amp;quot;\124cFFF00000\&amp;lt;Attempting to &amp;quot;..GetSpellInfo(sname[i])..&amp;quot; &amp;quot;..GetSpellInfo(sid)..&amp;quot; from &amp;quot;..sourceNAME..&amp;quot;&amp;gt;&amp;quot;, ChatTypeInfo[&amp;quot;RAID_WARNING&amp;quot;])
							TargetUnit(sourceNAME)
							CastSpellByID(sname[i])
							return
						end
						
						if sname[i] == Gouge       
						and cd == 0 
						and destGUID == UnitGUID(&amp;quot;player&amp;quot;) then
							SpellStopCasting()
							RaidNotice_AddMessage(RaidWarningFrame, &amp;quot;\124cFFF00000\&amp;lt;Attempting to &amp;quot;..GetSpellInfo(sname[i])..&amp;quot; &amp;quot;..GetSpellInfo(sid)..&amp;quot; from &amp;quot;..sourceNAME..&amp;quot;&amp;gt;&amp;quot;, ChatTypeInfo[&amp;quot;RAID_WARNING&amp;quot;])
							TargetUnit(sourceNAME)
							CastSpellByID(sname[i])
							return
						end
						
						if sname[i] == Dismantle        
						and cd == 0 
						and destGUID == UnitGUID(&amp;quot;player&amp;quot;) then
							SpellStopCasting()
							RaidNotice_AddMessage(RaidWarningFrame, &amp;quot;\124cFFF00000\&amp;lt;Attempting to &amp;quot;..GetSpellInfo(sname[i])..&amp;quot; &amp;quot;..GetSpellInfo(sid)..&amp;quot; from &amp;quot;..sourceNAME..&amp;quot;&amp;gt;&amp;quot;, ChatTypeInfo[&amp;quot;RAID_WARNING&amp;quot;])
							TargetUnit(sourceNAME)
							CastSpellByID(sname[i])
							return
						end
					end	
				end
			end
		 end
	end

	ToolkitFrame:RegisterEvent(&amp;quot;COMBAT_LOG_EVENT_UNFILTERED&amp;quot;)
	ToolkitFrame:SetScript(&amp;quot;OnEvent&amp;quot;, EventHandler)
	print(&amp;quot;Toolkit Loaded.&amp;quot;)
end</Lua><RecastDelay>0</RecastDelay><Target>Target</Target><CancelChannel>False</CancelChannel><LuaBefore></LuaBefore><LuaAfter></LuaAfter></Ability></ROGUE>
