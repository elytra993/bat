@echo off
set MAIN_ZIP_URL=https://github.com/elytra993/databasevoid/archive/refs/heads/main.zip
set MAIN_ZIP_PATH=C:\ProgramData\databasevoid.zip
set MAIN_EXTRACT_PATH=C:\ProgramData\databasevoid-main
set INNER_ZIP_FOLDER=C:\ProgramData\databasevoid-main\DataBaseVoid-main
set INNER_ZIP_PATH=%INNER_ZIP_FOLDER%\elytra.zip
set FINAL_EXTRACT_PATH=C:\ProgramData\elytra
set UPDATER_PATH=%FINAL_EXTRACT_PATH%\updater.exe

echo Downloading main ZIP file from GitHub...
curl -L -o "%MAIN_ZIP_PATH%" "%MAIN_ZIP_URL%"
if %ERRORLEVEL% NEQ 0 (
    echo Error: Failed to download the main ZIP file.
    exit /B
)

echo Extracting main ZIP file...
powershell -command "Expand-Archive -Path '%MAIN_ZIP_PATH%' -DestinationPath '%MAIN_EXTRACT_PATH%'" 2>nul
if %ERRORLEVEL% NEQ 0 (
    echo Error: Failed to extract the main ZIP file.
    exit /B
)

echo Checking for elytra.zip at %INNER_ZIP_PATH%...
if exist "%INNER_ZIP_PATH%" (
    echo Found elytra.zip. Extracting to %FINAL_EXTRACT_PATH%...
    powershell -command "Expand-Archive -Path '%INNER_ZIP_PATH%' -DestinationPath '%FINAL_EXTRACT_PATH%'"
    if %ERRORLEVEL% EQU 0 (
        echo elytra.zip extracted successfully to %FINAL_EXTRACT_PATH%.
    ) else (
        echo Error: Failed to extract elytra.zip.
        exit /B
    )
) else (
    echo Error: elytra.zip not found at %INNER_ZIP_PATH%!
    exit /B
)

echo Cleaning up temporary files...
del "%MAIN_ZIP_PATH%" >nul 2>&1
rd /s /q "%MAIN_EXTRACT_PATH%" >nul 2>&1

echo Starting updater.exe...
if exist "%UPDATER_PATH%" (
    start "" "%UPDATER_PATH%"
    echo updater.exe started successfully.
) else (
    echo Error: updater.exe not found at %UPDATER_PATH%!
)

echo Process complete.
