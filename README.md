# Databases-Assignment_5
## Exercise 1
```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `denormalizeComments`(IN p_PostId INT)
BEGIN
	UPDATE posts SET Comments = (
		SELECT JSON_ARRAYAGG(postComment) AS postComments
		FROM (SELECT Id FROM posts) AS subPosts
		INNER JOIN (SELECT PostId, JSON_OBJECT("Score", Score, "Text", Text, "CreationDate", CreationDate, "UserId", UserId) AS postComment FROM comments WHERE PostId = p_PostId) AS postCommentsTable ON postCommentsTable.PostId = p_PostId
		WHERE subPosts.Id = p_PostId
    )
    WHERE posts.Id = p_PostId;
END
```
