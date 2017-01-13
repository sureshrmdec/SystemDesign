```java
/*
Implement a simple twitter. Support the following method:

postTweet(user_id, tweet_text). Post a tweet.
getTimeline(user_id). Get the given user's most recently 10 tweets posted by himself, order by timestamp from most recent to least recent.
getNewsFeed(user_id). Get the given user's most recently 10 tweets in his news feed (posted by his friends and himself). Order by timestamp from most recent to least recent.
follow(from_user_id, to_user_id). from_user_id followed to_user_id.
unfollow(from_user_id, to_user_id). from_user_id unfollowed to to_user_id.
Have you met this question in a real interview? Yes
Example
postTweet(1, "LintCode is Good!!!")
>> 1
getNewsFeed(1)
>> [1]
getTimeline(1)
>> [1]
follow(2, 1)
getNewsFeed(2)
>> [1]
unfollow(2, 1)
getNewsFeed(2)
>> []


*/

/**
 * Definition of Tweet:
 * public class Tweet {
 *     public int id;
 *     public int user_id;
 *     public String text;
 *     public static Tweet create(int user_id, String tweet_text) {
 *         // This will create a new tweet object,
 *         // and auto fill id
 *     }
 * }
 */
public class MiniTwitter {


    class Node{
        int order;
        Tweet  tweet;
        public Node(int order, Tweet tweet){
            this.order = order;
            this.tweet = tweet;
        }
        
    }
    
    
    private Map<Integer, List<Node>> tweetMap;
    private Map<Integer, Map<Integer, Boolean>> friendMap ;
    private int order;
    
    
    private class SortByOrder implements Comparator<Node>{
        @Override
        public int compare(Node node1, Node node2){
            return node2.order - node1.order;
        }
    }
    
    
    
    public List<Node> getFirstTen(List<Node> temp){
        int last = 10;
        
        if(temp.size() < 10){
            last = temp.size();
        }
        
        return temp.subList(0, last);
    }
    
    
    public List<Node> getLastTen(List<Node> tmp) {
        int last = 10;
        if (tmp.size() < 10)
            last = tmp.size();
        return tmp.subList(tmp.size() - last, tmp.size());
    }
    
    
    
    
    public MiniTwitter() {
        // initialize your data structure here.
        this.tweetMap = new HashMap<Integer, List<Node>>();
        this.friendMap = new HashMap<Integer, Map<Integer, Boolean>>();
        
        this.order = 0;
    }

    // @param user_id an integer
    // @param tweet a string
    // return a tweet
    public Tweet postTweet(int user_id, String tweet_text) {
        //  Write your code here
        
        Tweet tweet = Tweet.create(user_id, tweet_text);
        
        if(!tweetMap.containsKey(user_id)){
            tweetMap.put(user_id, new ArrayList<Node>());
        }
        
        order += 1;
        
        tweetMap.get(user_id).add(new Node(order, tweet));
        
        return tweet;
    }

    // @param user_id an integer
    // return a list of 10 new feeds recently
    // and sort by timeline
    public List<Tweet> getNewsFeed(int user_id) {
        // Write your code here
        
        List<Node> temp = new ArrayList<Node>();
        
        if(tweetMap.containsKey(user_id)){
            temp.addAll(getLastTen(tweetMap.get(user_id)));
        }
        
        if(friendMap.containsKey(user_id)){
            for (Map.Entry<Integer, Boolean> entry : friendMap.get(user_id).entrySet()) {
                int user = entry.getKey();
                if (tweetMap.containsKey(user))
                    temp.addAll(getLastTen(tweetMap.get(user)));
            }
        }
        
        Collections.sort(temp, new SortByOrder());
        List<Tweet> rt = new ArrayList<Tweet>();
        temp = getFirstTen(temp);
        for (Node node : temp) {
            rt.add(node.tweet);
        }
        return rt;
        
    }
        
    // @param user_id an integer
    // return a list of 10 new posts recently
    // and sort by timeline
    public List<Tweet>  getTimeline(int user_id) {
        // Write your code here
        List<Node> tmp = new ArrayList<Node>();
        if (tweetMap.containsKey(user_id))
            tmp.addAll(getLastTen(tweetMap.get(user_id)));

        Collections.sort(tmp, new SortByOrder());
        List<Tweet> rt = new ArrayList<Tweet>();
        tmp = getFirstTen(tmp);
        for (Node node : tmp)
            rt.add(node.tweet);
        return rt;
    }

    // @param from_user_id an integer
    // @param to_user_id an integer
    // from user_id follows to_user_id
    public void follow(int from_user_id, int to_user_id) {
        // Write your code here
        if (!friendMap.containsKey(from_user_id))
            friendMap.put(from_user_id, new HashMap<Integer, Boolean>());

        friendMap.get(from_user_id).put(to_user_id, true);
    }

    // @param from_user_id an integer
    // @param to_user_id an integer
    // from user_id unfollows to_user_id
    public void unfollow(int from_user_id, int to_user_id) {
        // Write your code here
        if (friendMap.containsKey(from_user_id))
            friendMap.get(from_user_id).remove(to_user_id);
    }
}
```
