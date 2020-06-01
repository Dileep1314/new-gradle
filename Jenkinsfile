import com.cloudbees.plugins.credentials.SystemCredentialsProvider
import groovy.json.JsonOutput
import groovy.json.JsonSlurper
import hudson.util.Secret
import java.util.regex.Pattern
import org.jenkinsci.plugins.plaincredentials.impl.StringCredentialsImpl

Secret getStringCredential(String id) {
    Secret secret = SystemCredentialsProvider.getInstance().credentials.find {
        it in StringCredentialsImpl && it.id == id
    }?.secret
    if(!secret) {
        throw new RuntimeException("Could not find a String Credential with the id '${id}'")
    }
    secret
}

Integer findCommentIdByUser(String credential, String repository, String pr_number, String username, String expression = '') {
	Secret gh_token = getStringCredential(credential)
	String api_endpoint = "https://api.github.com/repos/${repository}/issues/${pr_number}/comments"
    Reader reader = new URL(api_endpoint).newReader(requestProperties: ['Authorization': "token ${gh_token}".toString(), 'Accept': 'application/vnd.github.v3+json'])
    new JsonSlurper().parse(reader).find {
        it?.user?.login == username && (
            !expression || Pattern.compile(expression, Pattern.MULTILINE | Pattern.DOTALL).matcher(it?.body).matches()
        )
    }?.id ?: 0
}

void postGHPRComment(String credential, String repository, String pr_number, String message, Integer comment_id = 0) {
	Secret gh_token = getStringCredential(credential)
	String method = 'POST'
	Map data = [
		body: message
	]
	String api_endpoint = "https://api.github.com/repos/${repository}/issues"
    if(comment_id) {
        api_endpoint += "/comments/${comment_id}"
	    method = 'PATCH'
	}
	else {
	    api_endpoint += "/${pr_number}/comments"
    }
	String result = new URL(api_endpoint).openConnection().with {
		doOutput = true
		requestMethod = method
		setRequestProperty('Authorization', "token ${gh_token}".toString())
		setRequestProperty('Accept', 'application/vnd.github.v3+json')
		outputStream.withWriter { writer ->
			writer << JsonOutput.toJson(data)
		}
		content.text
	}
    //nothing to do with result
}

String message = '''
# heading 1
This is a new kind of comment.
'''.trim()
String pattern = '^# heading 1.*'
int id = findCommentIdByUser('github-token', 'samrocketman/jervis-example-project', '6', 'samrocketman', pattern)
postGHPRComment('github-token', 'samrocketman/jervis-example-project', '6', message, id)
