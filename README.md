# Sandbox-RequestInterview
A simple script for citizens to request a PD interview instead of using 911 or discord.

Simply add the following to sandbox-police/client/police/component.lua:

RegisterNetEvent("Police:Client:Interview", function()
	TriggerServerEvent("Police:Server:RequestInterview")
end)


Then add the following to sandbox-police/server/police/police.lua:

** At the top of your file add: local interviewCooldowns = {}


RegisterNetEvent("Police:Server:RequestInterview", function()
    print("Police:Server:RequestInterview event triggered")
    local src = source
    local char = exports['sandbox-characters']:FetchCharacterSource(src)
    local pState = Player(src).state
		local now = os.time()
		if interviewCooldowns[src] and (now - interviewCooldowns[src]) < (60 * 5) then
			exports['sandbox-hud']:Notification(src, "error", "You must wait before requesting another interview.")
			return
		end
		interviewCooldowns[src] = now
		if pState.onDuty == "police" then
			local coords = GetEntityCoords(GetPlayerPed(src))
			exports["sandbox-base"]:ClientCallback(src, "EmergencyAlerts:GetStreetName", coords, function(location)
				local locStr = type(location) == "string" and location or (json and json.encode and json.encode(location) or tostring(location))
				exports['sandbox-mdt']:EmergencyAlertsCreate(
					"INTERVIEW",
					"Interview Arrival",
					{ "police_alerts" },
					location,
					{
						icon = "user-tie",
					},
					false, 
					{
						icon = 101,
						size = 1.0,
						color = 3,
						duration = (60 * 5),
						sound = false 
					},
					1
				)
				exports['sandbox-hud']:Notification(src, "info", "Success! Your interview request has been sent to dispatch.")
			end)
		end
	end)
		local now = os.time()
		if interviewCooldowns[src] and (now - interviewCooldowns[src]) < (60 * 5) then
			exports['sandbox-hud']:Notification(src, "error", "You must wait before requesting another interview.")
			return
		end
		interviewCooldowns[src] = now

