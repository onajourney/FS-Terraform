<div align="center">
    <img style="margin-bottom: -16px" src="/logo.png" alt="PhotonSCSS Logo" width="132" title="PhotonSCSS Logo"/>
    <h1 style="margin-top: 24px; margin-bottom: 4px">FS-Terraform</h1>
    File system based terraform.
    <br />
    For our complete documentation go <a href="https://onajourney.github.io/FS-Terraform/">here</a>.
</div>

# Installation

For playing around with aws infrastructure locally install LocalStack desktop and create a container.
Then deploy as you would.

`npm run init`

then plan or apply.

`npm run plan` or `npm run apply`

or use terraform directly within the infrastructure folder.

# Alternative - Use workflow for dev

`act -W '.github/workflows/deploy.yml' --var AWS_REGION=us-west-2 -s AWS_ACCESS_KEY=<key> -s AWS_secret_KEY=<key>`

# TODO
- IMPLEMENT TTL into dynamodb tables

# POSSIBLE IMPROVEMENTS
- SHARE A AN EXECUTION ROLE AND ONLY DIVERGE FROM SHARING WHEN NEEDED (policies need to be attached)
- Add versioning to s3 backend bucket
- Moved lambda event source mappins to the bottom (sometimes would encounter errors when switching streams off and on on a table) where the change would be triggered and move on to recreating the mapping but the mapping would occur before it has completed, this solution doesn't feel ideal - perhaps a polling null_resource [something like this](https://gist.github.com/onajourney/f2372c470017f21a4e9bcf4c497524cd) would be more appropriate. More likely though, this is a provider issue.
 
# Notes
- Lambda roles default to having a log policy, could be made optional
- Recommended to use ENV vars to pass backend s3 config (cant find a working example of this)

# Testing

<table>

  <tr>
    <th>Description</th>
    <th>Live Result</th>
  </tr>
  
  <tr>
    <td>
    stream_enabled: <span style="color: red">False</span><br />
      stream_view_type: <span style="color: red">None</span><br />
      lambda_permission: <span style="color: red">None</span><br />
      source_mapping: <span style="color: red">None</span>
    </td>
    <td><span style="color: red">Lambda Doesn't Run</span></td>
  </tr>
  
  <tr>
    <td>
    stream_enabled: <span style="color: green">True</span><br />
      stream_view_type: <span style="color: red">None</span><br />
      lambda_permission: <span style="color: red">None</span><br />
      source_mapping: <span style="color: red">None</span>
    </td>
    <td><span style="color: red">Lambda Doesn't Run</span><br /><span style="font-size: smaller; color: gray;">error: stream_view_type is required when stream_enabled = true</span></td>
  </tr>    
  
  <td>
    stream_enabled: <span style="color: red">False</span><br />
      stream_view_type: <span style="color: green">Present</span><br />
      lambda_permission: <span style="color: red">None</span><br />
      source_mapping: <span style="color: red">None</span>
    </td>
    <td><span style="color: red">Lambda Doesn't Run</span></td>
  </tr>
  
  <tr>
    <td>
    stream_enabled: <span style="color: green">True</span><br />
      stream_view_type: <span style="color: green">Present</span><br />
      lambda_permission: <span style="color: red">None</span><br />
      source_mapping: <span style="color: red">None</span>
    </td>
    <td><span style="color: red">Lambda Doesn't Run</span><br /><span style="font-size: smaller; color: gray;">deployment time: 13m40s</span></td>
  </tr>
  
    
  <tr>
    <td>
    stream_enabled: <span style="color: green">True</span><br />
      stream_view_type: <span style="color: green">Present</span><br />
      lambda_permission: <span style="color: green">Present</span><br />
      source_mapping: <span style="color: red">None</span>
    </td>
    <td><span style="color: red">Lambda Doesn't Run</span><br /><span style="font-size: smaller; color: gray;">deploys as long as a stream policy is present (with GetRecords, GetShardIterator, DescribeStream, and ListStreams)</span></td>
  </tr>
          
  <tr>
    <td>
    stream_enabled: <span style="color: green">True</span><br />
      stream_view_type: <span style="color: green">Present</span><br />
      lambda_permission: <span style="color: green">None</span><br />
      source_mapping: <span style="color: red">Present</span>
    </td>
    <td><span style="color: red">Lambda Doesn't Run</span><br /><span style="font-size: smaller; color: gray;">deploys as long as a stream policy is present (with GetRecords, GetShardIterator, DescribeStream, and ListStreams)</span></td>
  </tr>
      
  <tr>
    <td>
    stream_enabled: <span style="color: green">True</span><br />
      stream_view_type: <span style="color: green">Present</span><br />
      lambda_permission: <span style="color: green">Present</span><br />
      source_mapping: <span style="color: green">Present</span>
    </td>
    <td><span style="color: orange">Lambda Runs</span><br /><span style="font-size: smaller; color: gray;">(Only when the table is created with streams on - disabling and enabling on an existing table seems to break it)</span></td>
  </tr>

</table>
