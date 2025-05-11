# SendSMS-Using-API-in-SQL-Server
SendSMS Using API in SQL Server


SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

ALTER PROCEDURE [dbo].[SendSMS]
AS
BEGIN
    -- Note: Ensure the following options are enabled before running this procedure:
    -- EXEC sp_configure 'show advanced options', 1;
    -- RECONFIGURE;
    -- EXEC sp_configure 'Ole Automation Procedures', 1;
    -- RECONFIGURE;

    DECLARE @receptors VARCHAR(MAX) = '**************'; -- Receiver's phone number
    DECLARE @sms NVARCHAR(500) = N'اhello';             -- Message to send 
    DECLARE @sender VARCHAR(500) = '************';  -- Sender number
    DECLARE @apiKey VARCHAR(500) = '***********************';
    
    DECLARE @url VARCHAR(1000) =
        'https://api.kavenegar.com/v1/' + @apiKey +
        '/sms/send.json?receptor=' + @receptors +
        '&sender=' + @sender +
        '&message=' + @sms;

    PRINT @url; -- For debugging

    DECLARE @win INT;
    DECLARE @hr INT;
    DECLARE @responseText VARCHAR(8000);

    -- Create the HTTP request object
    EXEC @hr = sp_OACreate 'WinHttp.WinHttpRequest.5.1', @win OUT;
    IF @hr <> 0 EXEC sp_OAGetErrorInfo @win;

    -- Open a GET request
    EXEC @hr = sp_OAMethod @win, 'Open', NULL, 'GET', @url, 'false';
    IF @hr <> 0 EXEC sp_OAGetErrorInfo @win;

    -- Send the request
    EXEC @hr = sp_OAMethod @win, 'Send';
    IF @hr <> 0 EXEC sp_OAGetErrorInfo @win;

    -- Get the response
    EXEC @hr = sp_OAGetProperty @win, 'ResponseText', @responseText OUTPUT;
    IF @hr <> 0 EXEC sp_OAGetErrorInfo @win;

    -- Clean up
    EXEC @hr = sp_OADestroy @win;
    IF @hr <> 0 EXEC sp_OAGetErrorInfo @win;

    -- Print the response from Kavenegar API
    PRINT @responseText;
END
GO
