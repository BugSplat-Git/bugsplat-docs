# How to Upload Symbol Files with Symbol-Upload

## Overview 👀

Symbol-upload is a cross-platform application that automatically uploads [symbol files](../../introduction/development/working-with-symbol-files/) as part of your build process and is the successor of [SendPdbs](using-sendpdbs-to-automatically-upload-symbol-files.md). Each build of your product that sends crash reports must have an exact set of matching symbol files uploaded to BugSplat.

Prebuilt binaries of [symbol-upload](https://github.com/BugSplat-Git/symbol-upload) can be downloaded on the [GitHub releases page](https://github.com/BugSplat-Git/symbol-upload/releases). Alternatively, symbol-upload can be installed by [npm](https://npmjs.com/package/@bugsplat/symbol-upload) and used as a [CLI tool](https://github.com/BugSplat-Git/symbol-upload?tab=readme-ov-file#command-line) or a [javascript library](https://github.com/BugSplat-Git/symbol-upload?tab=readme-ov-file#api). We also provide a [GitHub Action](https://github.com/BugSplat-Git/symbol-upload?tab=readme-ov-file#action) that can be added to your build workflow.

{% hint style="warning" %}
If you download symbol-upload-macos via a web browser, Gatekeeper will block the application from running. To add the application to the system's allow list, run the following command in terminal

```bash
xattr -r -d com.apple.quarantine ./symbol-upload-macos
```
{% endhint %}

Feel free to send symbols to BugSplat for every build on your build/integration server. There is no limit on the number of symbols you can post to BugSplat. However, by default, each symbol file must be smaller than 4 GB.

A group of symbols identified by their application name and version is called a **symbol store**. Symbol-upload automatically creates a new symbol store each time you upload symbols to a unique application and version combination. BugSplat's backend automatically removes symbol stores that haven't been accessed recently. Using our web application, you can manually delete a symbol store.

## Using symbol-upload 🧑‍💻

Running symbol-upload in a command window without any arguments shows the following usage information:

```
@bugsplat/symbol-upload v8.0.5

  symbol-upload contains a command line utility and a set of libraries to help  
  you upload symbol files to BugSplat.                                          

Usage

  -h, --help                             Print this usage guide.                
  -b, --database string                  Your BugSplat database name. The value 
                                         of database must match the value used  
                                         to post crash reports. This value can  
                                         also be provided via the               
                                         BUGSPLAT_DATABASE environment          
                                         variable.                              
  -a, --application string               The name of your application. The      
                                         value of application must match the    
                                         value used to post crash reports. If   
                                         not provided symbol-upload will        
                                         attempt to use the value of the name   
                                         field in package.json if it exists in  
                                         the current working directory.         
  -v, --version string                   Your application's version. The value  
                                         of version must match the value used   
                                         to post crash reports. If not provided 
                                         symbol-upload will attempt to use the  
                                         value of the version field in          
                                         package.json if it exists in the       
                                         current working directory.             
  -u, --user string (optional)           The email address used to log into     
                                         your BugSplat account. If provided     
                                         --password must also be provided. This 
                                         value can also be provided via the     
                                         SYMBOL_UPLOAD_USER environment         
                                         variable.                              
  -p, --password string (optional)       The password for your BugSplat         
                                         account. If provided --user must also  
                                         be provided. This value can also be    
                                         provided via the                       
                                         SYMBOL_UPLOAD_PASSWORD environment     
                                         variable.                              
  -i, --clientId string (optional)       An OAuth2 Client Credentials Client ID 
                                         for the specified database. If         
                                         provided --clientSecret must also be   
                                         provided. This value can also be       
                                         provided via the                       
                                         SYMBOL_UPLOAD_CLIENT_ID environment    
                                         variable.                              
  -s, --clientSecret string (optional)   An OAuth2 Client Credentials Client    
                                         Secret for the specified database. If  
                                         provided --clientId must also be       
                                         provided. This value can also be       
                                         provided via the                       
                                         SYMBOL_UPLOAD_CLIENT_SECRET            
                                         environment variable.                  
  -r, --remove                           Removes symbols for a specified        
                                         database, application, and version. If 
                                         this option is provided no other       
                                         actions are taken.                     
  -f, --files string (optional)          Glob pattern that specifies a set of   
                                         files to upload. For example,          
                                         **/*.{pdb,exe,dll} will recursively    
                                         search for .pdb, .exe, and .dll files. 
                                         Defaults to '*.js.map'                 
  -d, --directory string (optional)      Path of the base directory used to     
                                         search for symbol files. This value    
                                         will be combined with the --files      
                                         glob. Defaults to '.'                  
  -m, --dumpSyms boolean (optional)      Use dump_syms to generate and upload   
                                         sym files for specified binaries.      

  The -u and -p arguments are not required if you set the environment variables 
  SYMBOL_UPLOAD_USER and SYMBOL_UPLOAD_PASSWORD, or provide a clientId and      
  clientSecret.                                                                 
                                                                                
  The -i and -s arguments are not required if you set the environment variables 
  SYMBOL_UPLOAD_CLIENT_ID and SYMBOL_UPLOAD_CLIENT_SECRET, or provide a user    
  and password.                                                                 

Example

  symbol-upload -b your-bugsplat-database -a your-application-name -v your-     
  version [ -f "*.js.map" -d "/path/to/containing/dir" [ -u your-bugsplat-email 
  -p your-bugsplat-password ] OR [ -i your-client-id -s your-client-secret] ]   

Links

  🐛 https://bugsplat.com                          
                                                   
  💻 https://github.com/BugSplat-Git/symbol-upload 
                                                   
  💌 support@bugsplat.com                          


```

### Authentication

Credentials can be provided to symbol-upload via the `-u` and `-p` command-line arguments. OAuth2 Client ID and Client Secret credentials can also be provided for authentication via the `-i` and `-s` arguments and are created on the [OAuth Integrations](https://app.bugsplat.com/v2/database/integrations#oauth) page.

### Example

The following is an example of how to invoke symbol-upload and search a directory recursively for `.dll`, `.pdb`, and `.exe` files. Replace the values of `your-bugsplat-database`, `your-email`, and `your-password` with your BugSplat database, email, and password. You can specify a [glob](https://github.com/isaacs/node-glob) for the `-f` argument to match for files based on a pattern.

```
symbol-upload -b your-bugsplat-database -a my-awesome-app -v 1.0 -u your-email -p your-password -d "/path/to/build -f "**/*.+(exe|dll|pdb)"
```

You can use the `-r` flag to remove a symbol store instead of uploading. This is helpful when you create a new build but don't want to increment the build number.

### Apple <a href="#improving-upload-speeds" id="improving-upload-speeds"></a>

MacOS and iOS builds typically generate `.app` or `.xcarchive` files. To upload bundled `.dSYM` files, point symbol-upload at the `.app` or `.xcarchive` file, and use a glob that instructs symbol-upload to search for `.dSYM` files recursively.

```
symbol-upload ... -d "/path/to/build.xcarchive -f "**/*.dSYM"
```

### Dump Syms <a href="#improving-upload-speeds" id="improving-upload-speeds"></a>

BugSplat can generate [Crashpad symbol files](https://github.com/google/breakpad/blob/master/docs/symbol_files.md) as part of the upload process. The Crashpad symbol files have a `.sym` format and are useful for cross-platform applications. BugSplat has integrated [Mozilla's dump-syms](https://github.com/mozilla/dump_syms) into symbol-upload, which allows developers to skip building Breakpad. To generate a `.sym` file at upload time, specify the `-m` flag when invoking symbol-upload.

### GitHub Actions

The symbol-upload repo also includes a GitHub Action that's compatible with the default Windows, macOS, and Linux images. Here's a snippet you can add to your workflow:

```yaml
- name: Symbols 📦
      uses: BugSplat-Git/symbol-upload@main
      with:
        clientId: "${{ secrets.SYMBOL_UPLOAD_CLIENT_ID }}"
        clientSecret: "${{ secrets.SYMBOL_UPLOAD_CLIENT_SECRET }}"
        database: "${{ secrets.BUGSPLAT_DATABASE }}"
        application: "MyConsoleCrasher"
        version: "1.0.0"
        files: "*.{pdb,exe,dll}"
        directory: "BugSplat\\Win32\\release"
```

We have also created an [example repo](https://github.com/BugSplat-Git/github-action-example) demonstrating how to use the @bugsplat/symbol-upload action to upload symbols to BugSplat.

### Improving Upload Speeds <a href="#improving-upload-speeds" id="improving-upload-speeds"></a>

Customers located far away from our US-East hosting location, especially those with high-latency and high-bandwidth connections, sometimes report slow upload speeds. We have several reports of significantly faster uploads after following the advice in the Microsoft technical note: [https://docs.microsoft.com/en-us/troubleshoot/windows-server/networking/tcpip-performance-known-issues](https://docs.microsoft.com/en-us/troubleshoot/windows-server/networking/tcpip-performance-known-issues)
